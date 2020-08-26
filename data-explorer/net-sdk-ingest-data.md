---
title: Прием данных с помощью пакета SDK Azure обозреватель данных .NET
description: Из этой статьи вы узнаете, как принимать (загружать) данные в Azure обозреватель данных с помощью пакета SDK для .NET.
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: c6a544228d5527f1703567256bd3e824ddc0504a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872715"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-sdk"></a>Прием данных с помощью пакета SDK Azure обозреватель данных .NET 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [GO](go-ingest-data.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Он предоставляет две клиентские библиотеки для .NET: [библиотеку приема](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/) и [библиотеку данных](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/). Дополнительные сведения о пакете SDK для .NET см. в разделе [о пакете SDK для .NET](/azure/data-explorer/kusto/api/netfx/about-the-sdk).
Они позволяют принимать (загружать) данные в кластер и запрашивать данные из кода. В этой статье вы сначала создадите таблицу и сопоставление данных в тестовом кластере. Затем вы поставите в очередь прием данных в кластер и проверите результаты.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

* [Тестовый кластер и база данных](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Установка библиотеки приема

```azurecli
Install-Package Microsoft.Azure.Kusto.Ingest
```

## <a name="add-authentication-and-construct-connection-string"></a>Добавление проверки подлинности и создание строки подключения

### <a name="authentication"></a>Аутентификация

Чтобы проверить подлинность приложения, Azure обозреватель данных SDK использует идентификатор клиента AAD. Чтобы найти идентификатор клиента, используйте следующий URL-адрес, заменив домен на *имя_вашего_домена*.

```http
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Например, если ваш домен называется *contoso.com*, URL-адрес будет следующим: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Щелкните этот URL-адрес, чтобы просмотреть результаты. Первая строка выглядит следующим образом: 

```console
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

В данном случае идентификатор клиента — `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

В этом примере для доступа к кластеру используется интерактивная проверка подлинности пользователя AAD. Вы также можете использовать проверку подлинности приложения AAD с секретом сертификата или приложения. `tenantId`Перед выполнением этого кода обязательно Задайте правильные значения для и `clusterUri` . 

Azure обозреватель данных SDK предоставляет удобный способ настройки метода проверки подлинности в составе строки подключения. Полную документацию по строкам подключения обозреватель данных Azure см. в разделе [строки подключения](kusto/api/connection-strings/kusto.md).

> [!NOTE]
> Текущая версия пакета SDK не поддерживает интерактивную проверку подлинности уер в .NET Core. При необходимости используйте имя пользователя AAD, пароль или проверку подлинности приложения.

### <a name="construct-the-connection-string"></a>Создание строки подключения

Теперь можно создать строку подключения обозреватель данных Azure. Целевая таблица и сопоставление будут созданы на более позднем этапе.

```csharp
var tenantId = "<TenantId>";
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net/";

var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri).WithAadUserPromptAuthentication(tenantId);
```

## <a name="set-source-file-information"></a>Определение данных исходного файла

Указание пути к исходному файлу. В этом примере используется пример файла, размещенный в хранилище BLOB-объектов Azure. Образец набора данных **стормевентс** содержит данные, связанные с погодой, из [национальных центров для информации о окружающей среде](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Создание таблицы в тестовом кластере

Создайте таблицу с именем `StormEvents`, которая соответствует схеме данных в файле `StormEvents.csv`.

> [!TIP]
> В следующих фрагментах кода создается экземпляр клиента для почти всех вызовов. Это делается для того, чтобы каждый фрагмент кода можно было использовать по отдельности. В рабочей среде экземпляры клиента являются повторными и должны сохраняться при необходимости. Достаточно одного экземпляра клиента на URI, даже при работе с несколькими базами данных (база данных может быть указана на уровне команды).

```csharp
var databaseName = "<DatabaseName>";
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-ingestion-mapping"></a>Определение сопоставления приема

Выполните сопоставление входящих данных CSV с именами столбцов, использованными при создании таблицы.
Подготавливает [объект сопоставления столбца CSV](kusto/management/create-ingestion-mapping-command.md) для этой таблицы.

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Csv,
            table,
            tableMapping,
            new[] {
                new ColumnMapping() { ColumnName = "StartTime", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "0" } } },
                new ColumnMapping() { ColumnName = "EndTime", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "1" } } },
                new ColumnMapping() { ColumnName = "EpisodeId", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "2" } } },
                new ColumnMapping() { ColumnName = "EventId", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "3" } } },
                new ColumnMapping() { ColumnName = "State", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "4" } } },
                new ColumnMapping() { ColumnName = "EventType", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "5" } } },
                new ColumnMapping() { ColumnName = "InjuriesDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "6" } } },
                new ColumnMapping() { ColumnName = "InjuriesIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "7" } } },
                new ColumnMapping() { ColumnName = "DeathsDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "8" } } },
                new ColumnMapping() { ColumnName = "DeathsIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "9" } } },
                new ColumnMapping() { ColumnName = "DamageProperty", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "10" } } },
                new ColumnMapping() { ColumnName = "DamageCrops", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "11" } } },
                new ColumnMapping() { ColumnName = "Source", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "12" } } },
                new ColumnMapping() { ColumnName = "BeginLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "13" } } },
                new ColumnMapping() { ColumnName = "EndLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "14" } } },
                new ColumnMapping() { ColumnName = "BeginLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "15" } } },
                new ColumnMapping() { ColumnName = "BeginLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "16" } } },
                new ColumnMapping() { ColumnName = "EndLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "17" } } },
                new ColumnMapping() { ColumnName = "EndLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "18" } } },
                new ColumnMapping() { ColumnName = "EpisodeNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "19" } } },
                new ColumnMapping() { ColumnName = "EventNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "20" } } },
                new ColumnMapping() { ColumnName = "StormSummary", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "21" } } }
        });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-batching-policy-for-your-table"></a>Определение политики пакетной обработки для таблицы

Прием обозреватель данных Azure выполняет пакетную обработку входящих данных для оптимизации размера сегмента данных. Этот процесс управляется [политикой пакетной обработки приема](kusto/management/batchingpolicy.md) , и ее можно изменить с помощью [команды управления политикой пакетной обработки](kusto/management/batching-policy.md). Используйте эту политику, чтобы сократить задержку медленных поступающих данных.

```kusto
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableAlterIngestionBatchingPolicyCommand(
        databaseName,
        table,
        new IngestionBatchingPolicy(maximumBatchingTimeSpan: TimeSpan.FromSeconds(10.0), maximumNumberOfItems: 100, maximumRawDataSizeMB: 1024));

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Отправка сообщения в очередь на прием

Поставьте в очередь сообщение, чтобы получить данные из хранилища BLOB-объектов и получить эти данные в обозреватель данных Azure. Подключение устанавливается к конечной точке приема данных в кластере Azure обозреватель данных, а для работы с этой конечной точкой создается другой клиент. 

> [!TIP]
> В следующих фрагментах кода создается экземпляр клиента для почти всех вызовов. Это делается для того, чтобы каждый фрагмент кода можно было использовать по отдельности. В рабочей среде экземпляры клиента являются повторными и должны сохраняться при необходимости. Достаточно одного экземпляра клиента на URI, даже при работе с несколькими базами данных (база данных может быть указана на уровне команды).

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net";
var ingestConnectionStringBuilder = new KustoConnectionStringBuilder(ingestUri).WithAadUserPromptAuthentication(tenantId);

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            IngestionMapping = new IngestionMapping()
            { 
                IngestionMappingReference = tableMapping,
                IngestionMappingKind = IngestionMappingKind.Csv
            },
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromStorageAsync(blobPath, ingestionProperties: properties).GetAwaiter().GetResult();
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Проверка получения данных в таблицу

Подождите от пяти до десяти минут для получения расписания приема и загрузки данных в Azure обозреватель данных. Затем выполните следующий код, чтобы получить количество записей в таблице `StormEvents`.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(databaseName, query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Выполнение запросов по устранению неполадок

Войдите в [https://dataexplorer.azure.com](https://dataexplorer.azure.com) кластер и подключитесь к нему. Выполните в своей базе данных следующую команду, чтобы проверить, не было ли в ней сбоев приема за последние четыре часа. Замените имя базы данных перед запуском.

```kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Выполните следующую команду, чтобы узнать состояние всех операций приема за последние четыре часа. Замените имя базы данных перед запуском.

```kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете следовать нашим другим статьям, не заполняйте созданные ресурсы. В противном случае выполните в своей базе данных следующую команду, чтобы очистить таблицу `StormEvents`.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Дальнейшие действия

* [Написание запросов](write-queries.md)
