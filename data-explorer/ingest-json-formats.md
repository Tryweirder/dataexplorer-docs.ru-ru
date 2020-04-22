---
title: Ingest JSON отформатировал данные в Azure Data Explorer
description: Узнайте о том, как глотать отформатированные jSON данные в Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 36c7616ee3d79cebaea96ca67787466cbd401cab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663182"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Ingest JSON отформатировал выборочные данные в Azure Data Explorer

В этой статье показано, как глотать отформатированные jSON данные в базу данных Azure Data Explorer. Вы начнете с простых примеров необработанных и отображенных JSON, продолжите многострочую JSON, а затем будете решать более сложные схемы JSON, содержащие массивы и словари.  В примерах подробно описывается процесс проема отформатированных jSON данных с помощью языка запросов Kusto (K'L), C' или Python. Команды управления языком `ingest` запроса Kusto выполняются непосредственно к конечному пункту двигателя. В производственных сценариях проглатывание выполняется в службу управления данными с использованием клиентских библиотек или соединений данных. Прочитайте [данные Ingest с помощью библиотеки Azure Data Explorer Python](/azure/data-explorer/python-ingest-data) и [данных Ingest с помощью Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) для прохождения в этих клиентских библиотеках.

## <a name="prerequisites"></a>Предварительные требования

[Кластер тестирования и база данных](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Формат JSON

Azure Data Explorer поддерживает два формата файлов JSON:
* `json`: Линия отделила JSON. Каждая строка в входных данных имеет ровно одну запись JSON.
* `multijson`: Многослойный JSON. Парсер игнорирует линейные сепараторы и читает запись от предыдущей позиции до конца действительного JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Проглокание и карта отформатированных данных JSON

Использование отформатированных данных JSON требует указания *формата* с помощью [свойства приема.](ingestion-properties.md) Использование данных JSON требует [отображения,](kusto/management/mappings.md)которое отображает запись источника JSON в целевой столбец. При глотании данных используйте заранее `jsonMappingReference` определенное `jsonMapping`свойство приема или укажите свойство приема. В этой статье `jsonMappingReference` будет использовано свойство приема, которое предварительно определено на столе, используемом для приема. В приведенных ниже примерах мы начнем с поимки записей JSON в качестве необработанных данных в одну таблицу столбцов. Затем мы будем использовать отображение для проема каждого свойства в его отображенном столбце. 

### <a name="simple-json-example"></a>Простой пример JSON

Следующим примером является простой JSON, с плоской структурой. Данные имеют информацию о температуре и влажности, собранную несколькими устройствами. Каждая запись отмечена идентификатором и меткой времени.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Проглоки сырых записей JSON 

В этом примере вы глотаете записи JSON в качестве необработанных данных в одну таблицу столбцов. Манипуляция данными, использование запросов и политика обновления выполняются после попавания данных.

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

Используйте язык запросов Kusto для поимки данных в сыром формате JSON.

1. Выполните вход на странице [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Выберите **Добавить кластер**.

1. В диалоговом окне **Добавить кластер** введите URL-адрес кластера в форму `https://<ClusterName>.<Region>.kusto.windows.net/`, а затем выберите **Добавить**.

1. Вставьте в следующую команду и выберите **Run** для создания таблицы.

    ```kusto
    .create table RawEvents (Event: dynamic)
    ```

    Этот запрос создает таблицу `Event` с одним столбцом [динамического](kusto/query/scalar-data-types/dynamic.md) типа данных.

1. Создайте отображение JSON.

    ```kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Эта команда создает отображение и `$` отображает `Event` путь корня JSON к столбику.

1. Проем данных `RawEvents` в таблицу.

    ```kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

Используйте C-е для глотания данных в сыром формате JSON.

1. Создайте `RawEvents` таблицу.

    ```csharp
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Создайте отображение JSON.
    
    ```csharp
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Эта команда создает отображение и `$` отображает `Event` путь корня JSON к столбику.

1. Проем данных `RawEvents` в таблицу.

    ```csharp
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Данные агрегируются в соответствии с [политикой пакетирования,](kusto/management/batchingpolicy.md)что приводит к задержке в несколько минут.

# <a name="python"></a>[Python](#tab/python)

Используйте Python для глотания данных в сыром формате JSON.

1. Создайте `RawEvents` таблицу.

    ```python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Создайте отображение JSON.

    ```python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Проем данных `RawEvents` в таблицу.

    ```python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Данные агрегируются в соответствии с [политикой пакетирования,](kusto/management/batchingpolicy.md)что приводит к задержке в несколько минут.

---

## <a name="ingest-mapped-json-records"></a>Проглотядные записи JSON

В этом примере вы глотаете данные записей JSON. Каждое свойство JSON отображается в одном столбце в таблице. 

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

1. Создайте новую таблицу с аналогичной схемой с входными данными JSON. Мы будем использовать эту таблицу для всех следующих примеров и глотание команд. 

    ```kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Создайте отображение JSON.

    ```kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    В этом отображении, как это `timestamp` определено схемой таблицы, `Time` записи будут познаваться в столбец в виде `datetime` типов данных.

1. Проем данных `Events` в таблицу.

    ```kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Файл 'simple.json' имеет несколько линейных записей JSON. `json`Формат, и отображение, используемое в `FlatEventMapping` глотании команды вы создали.

# <a name="c"></a>[C#](#tab/c-sharp)

1. Создайте новую таблицу с аналогичной схемой с входными данными JSON. Мы будем использовать эту таблицу для всех следующих примеров и глотание команд. 

    ```csharp
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Создайте отображение JSON.

    ```csharp
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    В этом отображении, как это `timestamp` определено схемой таблицы, `Time` записи будут познаваться в столбец в виде `datetime` типов данных.    

1. Проем данных `Events` в таблицу.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Файл 'simple.json' имеет несколько линейных записей JSON. `json`Формат, и отображение, используемое в `FlatEventMapping` глотании команды вы создали.

# <a name="python"></a>[Python](#tab/python)

1. Создайте новую таблицу с аналогичной схемой с входными данными JSON. Мы будем использовать эту таблицу для всех следующих примеров и глотание команд. 

    ```python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Создайте отображение JSON.

    ```python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Проем данных `Events` в таблицу.

    ```python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Файл 'simple.json' имеет несколько строк разделенных записей JSON. `json`Формат, и отображение, используемое в `FlatEventMapping` глотании команды вы создали.    
---

## <a name="ingest-multi-lined-json-records"></a>Глотать многоподкладные записи JSON

В этом примере вы глотаете многоподкладные записи JSON. Каждое свойство JSON отображается в одном столбце в таблице. Файл 'multilined.json' имеет несколько отступных записей JSON. Формат `multijson` говорит движку читать записи по структуре JSON.

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

Проем данных `Events` в таблицу.

```kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

Проем данных `Events` в таблицу.

```csharp
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="python"></a>[Python](#tab/python)

Проем данных `Events` в таблицу.

```python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Записи Ingest JSON, содержащие массивы

Тип данных "массив" представляет собой упорядоченную коллекцию значений. Загласка массива JSON осуществляется [по политике обновления.](kusto/management/update-policy.md) JSON попадает как есть в промежуточную таблицу. Политика обновления выполняет заранее определенную `RawEvents` функцию на столе, переназначая результаты в целевую таблицу. Мы будем глотать данные со следующей структурой:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

1. Создайте `update policy` функцию, которая `records` расширяет коллекцию так, чтобы каждое значение `mv-expand` в коллекции получало отдельную строку с помощью оператора. Мы будем использовать `RawEvents` таблицу как `Events` исходную таблицу и как целевую таблицу.

    ```kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Схема, полученная функцией, должна соответствовать схеме целевой таблицы. Используйте `getschema` оператора для просмотра схемы.

    ```kusto
    EventRecordsExpand() | getschema
    ```

1. Добавьте политику обновления для целевой таблицы. Эта политика автоматически запустит запрос на любые вновь `RawEvents` проникнутые данные `Events` в промежуточной таблице и будет вовлять результаты в таблицу. Определите политику нулевого удержания, чтобы избежать сохранения промежуточной таблицы.

    ```kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Проем данных `RawEvents` в таблицу.

    ```kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Просмотрите данные `Events` в таблице.

    ```kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Создайте функцию обновления, которая `records` расширяет коллекцию так, чтобы каждое `mv-expand` значение в коллекции получало отдельную строку с помощью оператора. Мы будем использовать `RawEvents` таблицу как `Events` исходную таблицу и как целевую таблицу.   

    ```csharp
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Схема, полученная функцией, должна соответствовать схеме целевой таблицы.

1. Добавьте политику обновления для целевой таблицы. Эта политика автоматически запустит запрос на любые вновь `RawEvents` проникнутые данные `Events` в промежуточной таблице и внигает его результаты в таблицу. Определите политику нулевого удержания, чтобы избежать сохранения промежуточной таблицы.

    ```csharp
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Проем данных `RawEvents` в таблицу.

    ```csharp
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Просмотрите данные `Events` в таблице.

# <a name="python"></a>[Python](#tab/python)

1. Создайте функцию обновления, которая `records` расширяет коллекцию так, чтобы каждое `mv-expand` значение в коллекции получало отдельную строку с помощью оператора. Мы будем использовать `RawEvents` таблицу как `Events` исходную таблицу и как целевую таблицу.   

    ```python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Схема, полученная функцией, должна соответствовать схеме целевой таблицы.

1. Добавьте политику обновления для целевой таблицы. Эта политика автоматически запустит запрос на любые вновь `RawEvents` проникнутые данные `Events` в промежуточной таблице и внигает его результаты в таблицу. Определите политику нулевого удержания, чтобы избежать сохранения промежуточной таблицы.

    ```python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Проем данных `RawEvents` в таблицу.

    ```python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Просмотрите данные `Events` в таблице.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Записи Ingest JSON, содержащие словари

Словарь структурированный JSON содержит пары ключевых значений. Записи Json проходят отображение `JsonPath`с помощью логического выражения в . Вы можете глотать данные со следующей структурой:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[Kql](#tab/kusto-query-language)

1. Создайте отображение JSON.

    ```kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Проем данных `Events` в таблицу.

    ```kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Создайте отображение JSON.

    ```csharp
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Проем данных `Events` в таблицу.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. Создайте отображение JSON.

    ```python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Проем данных `Events` в таблицу.

     ```python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Следующие шаги

* [Общие сведения о приеме данных](ingest-data-overview.md)
* [Написание запросов](write-queries.md)
