---
title: Прием данных с помощью Kusto. Принимающая библиотека — Azure обозреватель данных
description: В этой статье описывается прием данных с помощью библиотеки Kusto. приема Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: fe268d19e5f42308737b7c392c58c6c1dca071b3
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799617"
---
# <a name="data-ingestion-with-the-kustoingest-library"></a>Прием данных с помощью библиотеки Kusto. приема

В этой статье представлен пример кода, который использует клиентскую библиотеку Kusto. приема для приема данных. В коде описывается рекомендуемый режим приема для конвейеров рабочего уровня, называемый приемом в очереди. Для библиотеки Kusto. приема соответствующая сущность является интерфейсом [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) . Клиентский код взаимодействует со службой обозреватель данных Azure, публикуя уведомления об приеме в очередь Azure. Ссылка на очередь получена из Управление данными сущности, ответственной за прием. 

> [!NOTE]
> Взаимодействие со службой Управление данными должно проходить проверку подлинности с помощью Azure Active Directory (Azure AD).

В примере используется проверка подлинности пользователя Azure AD и выполняется с удостоверением интерактивного пользователя.

## <a name="dependencies"></a>Зависимости

Для этого примера кода требуются следующие пакеты NuGet:
* Microsoft. Kusto. прием
* Microsoft.IdentityModel.Clients.ActiveDirectory;
* WindowsAzure.Storage
* Newtonsoft.Json.

## <a name="namespaces-used"></a>Используемые пространства имен

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

Код выполняет следующие программы.
1. Создание таблицы в `KustoLab` общем кластере Azure обозреватель данных в `KustoIngestClientDemo` базе данных
2. Подготавливает [объект сопоставления столбца JSON](../../management/create-ingestion-mapping-command.md) для этой таблицы
3. Создает экземпляр [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) для службы `Ingest-KustoLab` управление данными
4. Настройка [кустокуеуединжестионпропертиес](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) с соответствующими параметрами приема
5. Создает объект MemoryStream, заполненный некоторыми созданными данными для приема
6. Принимает данные с помощью `KustoQueuedIngestClient.IngestFromStream` метода
7. Опрашивает о любых [ошибках приема](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)

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
