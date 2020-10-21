---
title: Прием данных в формате JSON в Azure обозреватель данных
description: Узнайте, как принимать данные в формате JSON в обозреватель данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: f599698f4b35075aaec4ff3789fa9036c40d8c17
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343238"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Получение демонстрационных данных в формате JSON в Azure обозреватель данных

В этой статье показано, как получать данные в формате JSON в базу данных Azure обозреватель данных. Вы начинаете с простых примеров необработанных и сопоставленных JSON, переходите к нескольким строкам JSON, а затем разберем более сложные схемы JSON, содержащие массивы и словари.  В примерах подробно рассматривается процесс приема данных в формате JSON с использованием языка запросов Kusto (ККЛ), C# или Python. Команды управления языком запросов Kusto `ingest` выполняются непосредственно в конечной точке обработчика. В рабочих сценариях прием выполняется в службе Управление данными с помощью клиентских библиотек или подключений к данным. Пошаговое руководство по приему данных для этих клиентских библиотек см. в статье прием данных с [помощью библиотеки azure обозреватель данных Python](python-ingest-data.md) и прием [данных с помощью пакета SDK для Azure обозреватель данных .NET Standard](./net-sdk-ingest-data.md) .

## <a name="prerequisites"></a>Обязательные условия

[Тестовый кластер и база данных](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Формат JSON

Azure обозреватель данных поддерживает два формата файлов JSON:
* `json`: JSON, разделенный строками. Каждая строка входных данных содержит только одну запись JSON.
* `multijson`: JSON с несколькими строками. Средство синтаксического анализа игнорирует разделители строк и считывает запись из предыдущей точки в конец допустимого JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Прием и отображение данных в формате JSON

Для приема данных в формате JSON необходимо указать *Формат* с помощью [Свойства приема](ingestion-properties.md). Для приема данных JSON требуется [сопоставление](kusto/management/mappings.md), которое сопоставляет исходную запись JSON со своим целевым столбцом. При приеме данных используйте `IngestionMapping` свойство со свойством `ingestionMappingReference` приема (для предварительно определенного сопоставления) или его `IngestionMappings` свойством. В этой статье будет использоваться `ingestionMappingReference` свойство приема, которое предварительно определено в таблице, используемой для приема. В приведенных ниже примерах мы начнем с приема записей JSON в виде необработанных данных в таблицу с одним столбцом. Затем мы будем использовать сопоставление для приема каждого свойства в сопоставленном столбце. 

### <a name="simple-json-example"></a>Пример простого JSON

В следующем примере показан простой код JSON с плоской структурой. Данные содержат сведения о температуре и влажности, собранные несколькими устройствами. Каждая запись помечается ИДЕНТИФИКАТОРом и меткой времени.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Прием необработанных записей JSON 

В этом примере вы принимаете записи JSON в виде необработанных данных в таблицу с одним столбцом. Обработка данных, использование запросов и политика обновления выполняются после приема данных.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Используйте язык запросов Kusto для приема данных в необработанном формате JSON.

1. Войдите на портал [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Выберите **Добавить кластер**.

1. В диалоговом окне **Добавить кластер** введите URL-адрес кластера в форму `https://<ClusterName>.<Region>.kusto.windows.net/`, а затем выберите **Добавить**.

1. Вставьте следующую команду и выберите **Run (выполнить** ), чтобы создать таблицу.

    ```kusto
    .create table RawEvents (Event: dynamic)
    ```

    Этот запрос создает таблицу с одним `Event` столбцом [динамического](kusto/query/scalar-data-types/dynamic.md) типа данных.

1. Создайте сопоставление JSON.

    ```kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","Properties":{"path":"$"}}]'
    ```

    Эта команда создает сопоставление и сопоставляет корневой путь JSON со `$` `Event` столбцом.

1. Прием данных в `RawEvents` таблицу.

    ```kusto
    .ingest into table RawEvents (h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D') with '{"format":json, "ingestionMappingReference":"DiagnosticRawRecordsMapping"}'
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

Используйте C# для приема данных в необработанном формате JSON.

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

1. Создайте сопоставление JSON.
    
    ```csharp
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            tableName,
            tableMapping,
            new[] {
            new ColumnMapping {ColumnName = "Events", Properties = new Dictionary<string, string>() {
                {"path","$"} }
            } });

    kustoClient.ExecuteControlCommand(command);
    ```
    Эта команда создает сопоставление и сопоставляет корневой путь JSON со `$` `Event` столбцом.

1. Прием данных в `RawEvents` таблицу.

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
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

> [!NOTE]
> Статистические данные вычисляются в соответствии с [политикой пакетной обработки](kusto/management/batchingpolicy.md), что приводит к задержке в несколько минут.

# <a name="python"></a>[Python](#tab/python)

Используйте Python для приема данных в необработанном формате JSON.

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

1. Создайте сопоставление JSON.

    ```python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Прием данных в `RawEvents` таблицу.

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
    > Статистические данные вычисляются в соответствии с [политикой пакетной обработки](kusto/management/batchingpolicy.md), что приводит к задержке в несколько минут.

---

## <a name="ingest-mapped-json-records"></a>Получение сопоставленных записей JSON

В этом примере вы принимаете данные записей JSON. Каждое свойство JSON сопоставляется с одним столбцом в таблице. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Создайте новую таблицу с аналогичной схемой входных данных JSON. Мы будем использовать эту таблицу для всех следующих примеров и команд приема. 

    ```kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Создайте сопоставление JSON.

    ```kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","Properties":{"path":"$.timestamp"}},{"column":"Device","Properties":{"path":"$.deviceId"}},{"column":"MessageId","Properties":{"path":"$.messageId"}},{"column":"Temperature","Properties":{"path":"$.temperature"}},{"column":"Humidity","Properties":{"path":"$.humidity"}}]'
    ```

    В этом сопоставлении, как определено схемой таблицы, `timestamp` записи будут приприниматься к столбцу `Time` как к `datetime` типам данных.

1. Прием данных в `Events` таблицу.

    ```kusto
    .ingest into table Events (h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D') with '{"format":"json", "ingestionMappingReference":"FlatEventMapping"}'
    ```

    Файл "simple.json" содержит несколько записей JSON, разделенных строками. Формат — `json` , а сопоставление, используемое в команде приема, — `FlatEventMapping` созданное вами.

# <a name="c"></a>[C#](#tab/c-sharp)

1. Создайте новую таблицу с аналогичной схемой входных данных JSON. Мы будем использовать эту таблицу для всех следующих примеров и команд приема. 

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

1. Создайте сопоставление JSON.

    ```csharp
    var tableMapping = "FlatEventMapping";
    var command =
         CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            "",
            tableMapping,
            new[]
            {
               new ColumnMapping() {ColumnName = "Time", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.timestamp"} } },
               new ColumnMapping() {ColumnName = "Device", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.deviceId" } } },
               new ColumnMapping() {ColumnName = "MessageId", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.messageId" } } },
               new ColumnMapping() {ColumnName = "Temperature", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.temperature" } } },
               new ColumnMapping() { ColumnName= "Humidity", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.humidity" } } },
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    В этом сопоставлении, как определено схемой таблицы, `timestamp` записи будут приприниматься к столбцу `Time` как к `datetime` типам данных.    

1. Прием данных в `Events` таблицу.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

    Файл "simple.json" содержит несколько записей JSON, разделенных строками. Формат — `json` , а сопоставление, используемое в команде приема, — `FlatEventMapping` созданное вами.

# <a name="python"></a>[Python](#tab/python)

1. Создайте новую таблицу с аналогичной схемой входных данных JSON. Мы будем использовать эту таблицу для всех следующих примеров и команд приема. 

    ```python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Создайте сопоставление JSON.

    ```python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","Properties":{"path":"$.timestamp"}},{"column":"Device","Properties":{"path":"$.deviceId"}},{"column":"MessageId","Properties":{"path":"$.messageId"}},{"column":"Temperature","Properties":{"path":"$.temperature"}},{"column":"Humidity","Properties":{"path":"$.humidity"}}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Прием данных в `Events` таблицу.

    ```python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Файл "simple.json" содержит несколько разделенных строк записей JSON. Формат — `json` , а сопоставление, используемое в команде приема, — `FlatEventMapping` созданное вами.    
---

## <a name="ingest-multi-lined-json-records"></a>Прием записей JSON с несколькими строками

В этом примере вы принимаете многострочные записи JSON. Каждое свойство JSON сопоставляется с одним столбцом в таблице. Файл "multilined.json" содержит несколько отступов в формате JSON. Формат `multijson` указывает обработчику читать записи по структуре JSON.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Прием данных в `Events` таблицу.

```kusto
.ingest into table Events (h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D') with '{"format":"multijson", "ingestionMappingReference":"FlatEventMapping"}'
```

# <a name="c"></a>[C#](#tab/c-sharp)

Прием данных в `Events` таблицу.

```csharp
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMapping = new IngestionMapping()
        {
            IngestionMappingReference = tableMapping
        }
    };

ingestClient.IngestFromStorageAsync(blobPath, properties);
```

# <a name="python"></a>[Python](#tab/python)

Прием данных в `Events` таблицу.

```python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Прием записей JSON, содержащих массивы

Тип данных "массив" представляет собой упорядоченную коллекцию значений. Прием массива JSON выполняется [политикой обновления](kusto/management/update-policy.md). JSON принимается "как есть" в промежуточную таблицу. Политика обновления запускает в таблице предварительно определенную функцию, возвращающую `RawEvents` результаты в целевую таблицу. Мы будем принимать данные со следующей структурой:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Создайте `update policy` функцию, которая расширяет коллекцию `records` таким образом, чтобы каждое значение в коллекции получало отдельную строку с помощью `mv-expand` оператора. Таблица будет использоваться в `RawEvents` качестве исходной таблицы и `Events` целевой таблицы.

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

1. Схема, полученная функцией, должна соответствовать схеме целевой таблицы. `getschema`Для просмотра схемы используйте оператор.

    ```kusto
    EventRecordsExpand() | getschema
    ```

1. Добавьте политику обновления для целевой таблицы. Эта политика автоматически запускает запрос для всех вновь принимаемых данных в `RawEvents` промежуточной таблице и принимает результаты в `Events` таблицу. Определите политику нулевого хранения, чтобы избежать сохранения промежуточной таблицы.

    ```kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Прием данных в `RawEvents` таблицу.

    ```kusto
    .ingest into table RawEvents (h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D') with '{"format":"multijson", "ingestionMappingReference":"RawEventMapping"}'
    ```

1. Проверьте данные в `Events` таблице.

    ```kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Создайте функцию Update, которая расширяет коллекцию `records` таким образом, чтобы каждое значение в коллекции получало отдельную строку с помощью `mv-expand` оператора. Таблица будет использоваться в `RawEvents` качестве исходной таблицы и `Events` целевой таблицы.   

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

1. Добавьте политику обновления для целевой таблицы. Эта политика автоматически запустит запрос для всех вновь принимаемых данных в `RawEvents` промежуточной таблице и будет принимать результаты в `Events` таблицу. Определите политику нулевого хранения, чтобы избежать сохранения промежуточной таблицы.

    ```csharp
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Прием данных в `RawEvents` таблицу.

    ```csharp
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```
    
1. Проверьте данные в `Events` таблице.

# <a name="python"></a>[Python](#tab/python)

1. Создайте функцию Update, которая расширяет коллекцию `records` таким образом, чтобы каждое значение в коллекции получало отдельную строку с помощью `mv-expand` оператора. Таблица будет использоваться в `RawEvents` качестве исходной таблицы и `Events` целевой таблицы.   

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

1. Добавьте политику обновления для целевой таблицы. Эта политика автоматически запустит запрос для всех вновь принимаемых данных в `RawEvents` промежуточной таблице и будет принимать результаты в `Events` таблицу. Определите политику нулевого хранения, чтобы избежать сохранения промежуточной таблицы.

    ```python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Прием данных в `RawEvents` таблицу.

    ```python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Проверьте данные в `Events` таблице.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Принимающие записи JSON, содержащие словари

JSON, структурированный по словарю, содержит пары "ключ-значение". JSON регистрирует сопоставление приема с помощью логического выражения в `JsonPath` . Вы можете принимать данные со следующей структурой:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Создайте сопоставление JSON.

    ```kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"a","Properties":{"path":"$.event[?(@.Key == \'timestamp\')]"}},{"column":"b","Properties":{"path":"$.event[?(@.Key == \'deviceId\')]"}},{"column":"c","Properties":{"path":"$.event[?(@.Key == \'messageId\')]"}},{"column":"d","Properties":{"path":"$.event[?(@.Key == \'temperature\')]"}},{"column":"Humidity","datatype":"string","Properties":{"path":"$.event[?(@.Key == \'humidity\')]"}}]'
    ```

1. Прием данных в `Events` таблицу.

    ```kusto
    .ingest into table Events (h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D') with '{"format":"multijson", "ingestionMappingReference":"KeyValueEventMapping"}'
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Создайте сопоставление JSON.

    ```csharp
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
         CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            "",
            tableMapping,
            new[]
            {
                new ColumnMapping() { ColumnName = "Time", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'timestamp')]"
                } } },
                    new ColumnMapping() { ColumnName = "Device", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'deviceId')]"
                } } }, new ColumnMapping() { ColumnName = "MessageId", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'messageId')]"
                } } }, new ColumnMapping() { ColumnName = "Temperature", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'temperature')]"
                } } }, new ColumnMapping() { ColumnName = "Humidity", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'humidity')]"
                } } },
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Прием данных в `Events` таблицу.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };
    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. Создайте сопоставление JSON.

    ```python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Прием данных в `Events` таблицу.

     ```python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Дальнейшие шаги

* [Общие сведения о приеме данных](ingest-data-overview.md)
* [Написание запросов](write-queries.md)