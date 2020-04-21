---
title: Как Загласить данные с библиотекой Kusto.Ingest - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается, как проглатывается данные с помощью библиотеки Kusto.Ingest в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 80b2b61c70269c5bd166a064fe9d0e2c59dd8197
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523636"
---
# <a name="howto-data-ingestion-with-kustoingest-library"></a>Как Заглом от данных с библиотекой Kusto.Ingest
В этой статье представлен пример кода, который использует клиентскую библиотеку Kusto.Ingest.

## <a name="overview"></a>Обзор
Следующий пример кода демонстрирует, что в очередь (через службу управления данными Kusto) в Кусто с помощью библиотеки Kusto.Ingest попадает в очередь (идя через службу управления данными Kusto).

> В этой статье рассматривается рекомендуемый режим приема для конвейеров производственного класса, который также называется **«Квейтing Ingestion»** (с точки зрения библиотеки Kusto.Ingest соответствующим органом является интерфейс [IKusto'uedIngClient).](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) В этом режиме клиентский код взаимодействует с службой Kusto, размещая сообщения уведомлений о водоотданном сообщении в очередь Azure, ссылка на которую получена из Управления данными Kusto (также известная как Компания. Проглатывание) обслуживание. Взаимодействие со службой управления данными должно быть проверено с **помощью AAD.**

#### <a name="authentication"></a>Аутентификация
Этот пример кода использует аутентификацию пользователя AAD и работает под удостоверением интерактивного пользователя.

## <a name="dependencies"></a>Зависимости
Этот пример кода требует следующих пакетов NuGet:
* Microsoft.Kusto.Ingest
* Microsoft.IdentityModel.Clients.ActiveDirectory;
* WindowsAzure.Storage
* Newtonsoft.Json.

## <a name="namespaces-used"></a>Используемые именные пространства
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Kusto.Data;
using Kusto.Data.Common;
using Kusto.Data.Net.Client;
using Kusto.Ingest;
```

## <a name="code"></a>Код
Приведенный ниже код выполняет следующее:
1. Создает таблицу `KustoLab` на общем кластере Kusto под `KustoIngestClientDemo` базой данных
2. Предусматривает [объект отображения столбца JSON](../../management/create-ingestion-mapping-command.md) на этом столе
3. Создает экземпляр [IKusto'uingingingClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) для службы `Ingest-KustoLab` управления данными
4. Настройка [Kusto-ОчередьIngestionСвойства Свойства](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) с соответствующими вариантами приема
5. Создает MemoryStream, наполненный некоторыми генерируемыми данными для поимки
6. Прием данных с `KustoQueuedIngestClient.IngestFromStream` помощью метода
7. Опросы для любых [ошибок приема](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)

```csharp
static void Main(string[] args)
{
    var clusterName = "KustoLab";
    var db = "KustoIngestClientDemo";
    var table = "Table1";
    var mappingName = "Table1_mapping_1";
    var serviceNameAndRegion = "clusterNameAndRegion"; // For example, "mycluster.westus"
    var authority = "AAD Tenant or name"; // For example, "microsoft.com"

    // Set up table
    var kcsbEngine =
        new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var kustoAdminClient = KustoClientFactory.CreateCslAdminProvider(kcsbEngine))
    {
        var columns = new List<Tuple<string, string>>()
        {
            new Tuple<string, string>("Column1", "System.Int64"),
            new Tuple<string, string>("Column2", "System.DateTime"),
            new Tuple<string, string>("Column3", "System.String"),
        };

        var command = CslCommandGenerator.GenerateTableCreateCommand(table, columns);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);

        // Set up mapping
        var columnMappings = new List<JsonColumnMapping>();
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column1", JsonPath = "$.Id" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column2", JsonPath = "$.Timestamp" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column3", JsonPath = "$.Message" });

        command = CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
                                            table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);
    }

    // Create Ingest Client
    var kcsbDM =
        new KustoConnectionStringBuilder($"https://ingest-{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(kcsbDM))
    {
        var ingestProps = new KustoQueuedIngestionProperties(db, table);
        // For the sake of getting both failure and success notifications we set this to IngestionReportLevel.FailuresAndSuccesses
        // Usually the recommended level is IngestionReportLevel.FailuresOnly
        ingestProps.ReportLevel = IngestionReportLevel.FailuresAndSuccesses;
        ingestProps.ReportMethod = IngestionReportMethod.Queue;
        ingestProps.JSONMappingReference = mappingName;
        ingestProps.Format = DataSourceFormat.json;

        // Prepare data for ingestion
        using (var memStream = new MemoryStream())
        using (var writer = new StreamWriter(memStream))
        {
            for (int counter = 1; counter <= 10; ++counter)
            {
                writer.WriteLine(
                    "{{ \"Id\":\"{0}\", \"Timestamp\":\"{1}\", \"Message\":\"{2}\" }}",
                    counter, DateTime.UtcNow.AddSeconds(100 * counter),
                    $"This is a dummy message number {counter}");
            }

            writer.Flush();
            memStream.Seek(0, SeekOrigin.Begin);

            // Post ingestion message
            ingestClient.IngestFromStream(memStream, ingestProps, leaveOpen: true);
        }

        // Wait and retrieve all notifications
        //  - Actual duration should be decided based on the effective Ingestion Batching Policy set on the table/database
        Thread.Sleep(<timespan>);
        var errors = ingestClient.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```