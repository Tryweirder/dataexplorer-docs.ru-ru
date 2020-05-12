---
title: Kusto. прием клиентских интерфейсов и классов фабрики — Azure обозреватель данных
description: В этой статье описывается Kusto. приема клиентских интерфейсов и классов фабрики в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1d3c3939a5c8b3a5f1e6f1fa0b40f9b927ee5325
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226063"
---
# <a name="kustoingest-client-interfaces-and-factory-classes"></a>Kusto. приема клиентских интерфейсов и классов фабрики

Основные интерфейсы и классы фабрик в библиотеке Kusto. приема:

* [интерфейс икустоинжестклиент](#interface-ikustoingestclient): основной интерфейс приема.
* [Класс екстендедкустоинжестклиент](#class-extendedkustoingestclient): расширения основного интерфейса приема.
* [класс кустоинжестфактори](#class-kustoingestfactory): основная фабрика для клиентов приема.
* [класс кустоинжестионпропертиес](#class-kustoingestionproperties): класс, используемый для предоставления общих свойств приема.
* [Класс жсонколумнмаппинг](#class-jsoncolumnmapping): класс, используемый для описания сопоставления схемы, применяемого при приеме из источника данных JSON.
* [Класс ксвколумнмаппинг](#class-csvcolumnmapping): класс, используемый для описания сопоставления схемы, применяемого при приеме из источника данных CSV.
* [Enum датасаурцеформат](#enum-datasourceformat): Поддерживаемые форматы источников данных (например, CSV, JSON)
* [Интерфейс икустокуеуединжестклиент](#interface-ikustoqueuedingestclient): интерфейс, описывающий операции, которые применяются только для приема в очереди.
* [Класс кустокуеуединжестионпропертиес](#class-kustoqueuedingestionproperties): свойства, которые применяются только к постановке в очередь.

## <a name="interface-ikustoingestclient"></a>Икустоинжестклиент интерфейса

* инжестфромдатареадерасинк
* инжестфромсторажеасинк
* инжестфромстреамасинк

```csharp
public interface IKustoIngestClient : IDisposable
{
    /// <summary>
    /// Ingests data from <see cref="IDataReader"/>. <paramref name="dataReader"/> will be closed when the call completes.
    /// </summary>
    /// <param name="dataReader">The <see cref="IDataReader"/> data source to ingest. Only the first record set will be used</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="sourceOptions">Options for the <see cref="IDataReader"/> ingestion source. This is an optional parameter</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    Task<IKustoIngestionResult> IngestFromDataReaderAsync(IDataReader dataReader, KustoIngestionProperties ingestionProperties, DataReaderSourceOptions sourceOptions = null);

    /// <summary>
    /// Ingest data from one of the supported storage providers. Currently the supported providers are: File System, Azure Blob Storage.
    /// </summary>
    /// <param name="uri">The URI of the storage resource to be ingested. Note: This URI may include a storage account key or shared access signature (SAS).
    ///  See <see href="https://docs.microsoft.com/azure/kusto/api/connection-strings/storage"/> for the URI format options.</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="sourceOptions">Options for the storage ingestion source. This is an optional parameter</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    Task<IKustoIngestionResult> IngestFromStorageAsync(string uri, KustoIngestionProperties ingestionProperties, StorageSourceOptions sourceOptions = null);

    /// <summary>
    /// Ingest data from <see cref="Stream"/>.
    /// </summary>
    /// <param name="stream">The <see cref="Stream"/> data source to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="sourceOptions">Options for the <see cref="Stream"/> ingestion source. This is an optional parameter</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    Task<IKustoIngestionResult> IngestFromStreamAsync(Stream stream, KustoIngestionProperties ingestionProperties, StreamSourceOptions sourceOptions = null);
}
```

## <a name="class-extendedkustoingestclient"></a>Класс Екстендедкустоинжестклиент

* Инжестфромсинглеблоб — не рекомендуется. Используйте вместо этого `IKustoIngestClient.IngestFromStorageAsync`.
* Инжестфромсинглеблобасинк — не рекомендуется. Используйте вместо этого `IKustoIngestClient.IngestFromStorageAsync`.
* Инжестфромдатареадер — не рекомендуется. Используйте вместо этого `IKustoIngestClient.IngestFromDataReaderAsync`.
* инжестфромдатареадерасинк
* Инжестфромсинглефиле — не рекомендуется. Используйте вместо этого `IKustoIngestClient.IngestFromStorageAsync`.
* Инжестфромсинглефилеасинк — не рекомендуется. Используйте вместо этого `IKustoIngestClient.IngestFromStorageAsync`.
* Инжестфромстреам — не рекомендуется. Используйте вместо этого `IKustoIngestClient.IngestFromStreamAsync`.
* инжестфромстреамасинк

```csharp
public static class ExtendedKustoIngestClient
{
    /// <summary>
    /// Ingest data from a single data blob
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobUri">The URI of the blob will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleBlob(this IKustoIngestClient client, string blobUri, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from a single data blob asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobUri">The URI of the blob will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleBlobAsync(this IKustoIngestClient client, string blobUri, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from a single data blob
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobDescription"><see cref="BlobDescription"/> representing the blobs that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleBlob(this IKustoIngestClient client, BlobDescription blobDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from a single data blob asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="blobDescription"><see cref="BlobDescription"/> representing the blobs that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source blob should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="rawDataSize">The uncompressed raw data size</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleBlobAsync(this IKustoIngestClient client, BlobDescription blobDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties, long? rawDataSize = null);

    /// <summary>
    /// Ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReader">The data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromDataReader(this IKustoIngestClient client, IDataReader dataReader, KustoIngestionProperties ingestionProperties);

    /// <summary>
    ///  Asynchronously ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReader">The data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromDataReaderAsync(this IKustoIngestClient client, IDataReader dataReader, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReaderDescription"><see cref="DataReaderDescription"/>Represents the data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromDataReader(this IKustoIngestClient client, DataReaderDescription dataReaderDescription, KustoIngestionProperties ingestionProperties);

    /// <summary>
    ///  Asynchronously ingest data from <see cref="IDataReader"/>, which is closed and disposed of upon call completion
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="dataReaderDescription"><see cref="DataReaderDescription"/>Represents the data to ingest (only the first record set will be used)</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromDataReaderAsync(this IKustoIngestClient client, DataReaderDescription dataReaderDescription, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="filePath">Absolute path of the source file to be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleFile(this IKustoIngestClient client, string filePath, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="filePath">Absolute path of the source file to be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleFileAsync(this IKustoIngestClient client, string filePath, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="fileDescription"><see cref="FileDescription"/> representing the file that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromSingleFile(this IKustoIngestClient client, FileDescription fileDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from a single file asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="fileDescription"><see cref="FileDescription"/> representing the file that will be ingested</param>
    /// <param name="deleteSourceOnSuccess">Indicates if the source file should be deleted after a successful ingestion</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromSingleFileAsync(this IKustoIngestClient client, FileDescription fileDescription, bool deleteSourceOnSuccess, KustoIngestionProperties ingestionProperties);

    /// <summary>
    /// Ingest data from <see cref="Stream"/>
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="stream">The data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), <paramref name="stream"/> will be closed and disposed on call completion</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromStream(this IKustoIngestClient client, Stream stream, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);

    /// <summary>
    /// Ingest data from <see cref="Stream"/> asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="stream">The data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), <paramref name="stream"/> will be closed and disposed on call completion</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromStreamAsync(this IKustoIngestClient client, Stream stream, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);

    /// <summary>
    /// Ingest data from <see cref="Stream"/>
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="streamDescription"><see cref="StreamDescription"/>Represents the data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), streamDescription.Stream will be closed and disposed on call completion</param>
    /// <returns><see cref="IKustoIngestionResult"/></returns>
    public static IKustoIngestionResult IngestFromStream(this IKustoIngestClient client, StreamDescription streamDescription, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);

    /// <summary>
    /// Ingest data from <see cref="Stream"/> asynchronously
    /// </summary>
    /// <param name="client">The ingest client that will execute the ingestions</param>
    /// <param name="streamDescription"><see cref="StreamDescription"/>Represents the data to ingest</param>
    /// <param name="ingestionProperties">Additional properties to be used during the ingestion process</param>
    /// <param name="leaveOpen">Optional. If set to 'false' (default value), streamDescription.Stream will be closed and disposed on call completion</param>
    /// <returns>An <see cref="IKustoIngestionResult"/> task</returns>
    public static Task<IKustoIngestionResult> IngestFromStreamAsync(this IKustoIngestClient client, StreamDescription streamDescription, KustoIngestionProperties ingestionProperties, bool leaveOpen = false);
}
```

## <a name="class-kustoingestfactory"></a>Класс Кустоинжестфактори

* креатедиректинжестклиент
* креатекуеуединжестклиент
* креатеманажедстреамингинжестклиент
* креатестреамингинжестклиент

```csharp
/// <summary>
/// Factory for creating Kusto ingestion objects.
/// </summary>
public static class KustoIngestFactory
{
    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.
    /// </summary>
    /// <param name="kcsb">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.</returns>
    /// <remarks>In most cases, it is preferred that ingestion be done using the
    /// queued implementation of <see cref="IKustoIngestClient"/>. See <see cref="CreateQueuedIngestClient(KustoConnectionStringBuilder)"/>.</remarks>
    public static IKustoIngestClient CreateDirectIngestClient(KustoConnectionStringBuilder kcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.
    /// </summary>
    /// <param name="connectionString">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that communicates
    /// directly with the Kusto engine service.</returns>
    /// <remarks>In most cases, it is preferred that ingestion be done using the
    /// queued implementation of <see cref="IKustoIngestClient"/>. See <see cref="CreateQueuedIngestClient(string)"/>.</remarks>
    public static IKustoIngestClient CreateDirectIngestClient(string connectionString);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoQueuedIngestClient"/> that communicates
    /// with the Kusto ingestion service using a reliable queue.
    /// </summary>
    /// <param name="kcsb">Indicates the connection to the Kusto ingestion service.
    /// Note that the ingestion service generally has a "ingest-" prefix in the
    /// DNS host name part.</param>
    /// <returns>An implementation of <see cref="IKustoQueuedIngestClient"/> that communicates
    /// with the Kusto ingestion service using a reliable queue.</returns>
    public static IKustoQueuedIngestClient CreateQueuedIngestClient(KustoConnectionStringBuilder kcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoQueuedIngestClient"/> that communicates
    /// with the Kusto ingestion service using a reliable queue.
    /// </summary>
    /// <param name="connectionString">Indicates the connection to the Kusto ingestion service.
    /// Note that the ingestion service generally has a "ingest-" prefix in the
    /// DNS host name part.</param>
    /// <returns>An implementation of <see cref="IKustoQueuedIngestClient"/> that communicates with the Kusto ingestion service using a reliable queue.</returns>
    public static IKustoQueuedIngestClient CreateQueuedIngestClient(string connectionString);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion
    /// </summary>
    /// <param name="engineKcsb">Indicates the connection to the Kusto engine service.</param>
    /// <param name="dmKcsb">Indicates the connection to the Kusto data management service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy intto Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data.
    /// If the streaming ingset doesn't succeed after several retries, queued ingestion will be performed.</remarks>
    public static IKustoIngestClient CreateManagedStreamingIngestClient(KustoConnectionStringBuilder engineKcsb, KustoConnectionStringBuilder dmKcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion
    /// </summary>
    /// <param name="engineConnectionString">Indicates the connection to the Kusto engine service.</param>
    /// <param name="dmConnectionString">Indicates the connection to the Kusto data management service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs managed streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy intto Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data.
    /// If the streaming ingset doesn't succeed after several retries, queued ingestion will be performed.</remarks>
    public static IKustoIngestClient CreateManagedStreamingIngestClient(string engineConnectionString, string dmConnectionString);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion
    /// </summary>
    /// <param name="kcsb">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy intto Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data</remarks>
    public static IKustoIngestClient CreateStreamingIngestClient(KustoConnectionStringBuilder kcsb);

    /// <summary>
    /// Creates an implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion
    /// </summary>
    /// <param name="connectionString">Indicates the connection to the Kusto engine service.</param>
    /// <returns>An implementation of <see cref="IKustoIngestClient"/> that performs streaming ingestion</returns>
    /// <remarks>Streaming ingestion is performed directy into Kusto enginge cluster 
    /// and is optimized for low-latency ingestion of relatively small chunks of data</remarks>
    public static IKustoIngestClient CreateStreamingIngestClient(string connectionString);
}
```

## <a name="class-kustoingestionproperties"></a>Класс Кустоинжестионпропертиес

Класс Кустоинжестионпропертиес содержит базовые свойства приема для тонкого контроля над процессом приема и способ, которым механизм Kusto обрабатывает его.

|Свойство   |Значение    |
|-----------|-----------|
|имя_базы_данных |Имя базы данных для приема |
|TableName |Имя таблицы для приема |
|дропбитагс |Теги, которые будут иметься в каждом экстенте. Дропбитагс являются постоянными и могут использоваться следующим образом: `.show table T extents where tags has 'some tag'` или`.drop extents <| .show table T extents where tags has 'some tag'` |
|инжестбитагс |Теги, записываемые на экстент. Позднее можно использовать со `IngestIfNotExists` свойством, чтобы избежать одновременного приема одних и тех же данных |
|аддитионалтагс |Дополнительные теги по мере необходимости |
|инжестифнотексистс |Список тегов, которые не нужно принимать повторно (для каждой таблицы) |
|ксвмаппинг |Для каждого столбца определяет тип данных и порядковый номер столбца. Релевантно только для приема CSV (необязательно) |
|жсонмаппинг |Для каждого столбца определяет путь JSON и параметры преобразования. **Обязательно для приема JSON** |
|авромаппинг |Для каждого столбца определяет имя поля в записи Avro. **Обязательно для приема AVRO** |
|валидатионполици |Определения проверки данных. Дополнительные сведения см. в разделе [TODO] |
|Формат |Формат приема данных |
|AdditionalProperties | Другие свойства, которые будут переданы в качестве [свойств приема](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) в команду приема. Свойства будут передаваться, так как не все свойства приема представлены в отдельном члене этого класса|

```csharp
public class KustoIngestionProperties
{
    public string DatabaseName { get; set; }
    public string TableName { get; set; }
    public IEnumerable<string> DropByTags { get; set; }
    public IEnumerable<string> IngestByTags { get; set; }
    public IEnumerable<string> AdditionalTags { get; set; }
    public IEnumerable<string> IngestIfNotExists { get; set; }
    public IEnumerable<CsvColumnMapping> CSVMapping { get; set; }
    public IEnumerable<JsonColumnMapping> JsonMapping { get; set; } // Must be set for DataSourceFormat.json format
    public IEnumerable<AvroColumnMapping> AvroMapping { get; set; } // Must be set for DataSourceFormat.avro format
    public ValidationPolicy ValidationPolicy { get; set; }
    public DataSourceFormat? Format { get; set; }
    public bool IgnoreSizeLimit { get; set; } // Determines whether the limit of 4GB per single ingestion source should be ignored. Defaults to false.
    public IDictionary<string, string> AdditionalProperties { get; set; }

    public KustoIngestionProperties(string databaseName, string tableName);
}
```

## <a name="class-jsoncolumnmapping"></a>Класс Жсонколумнмаппинг

```csharp
public class JsonColumnMapping
{
    /// The column name (in the Kusto table)
    public string ColumnName { get; set; }

    /// The JsonPath to the desired property in the JSON document
    public string JsonPath { get; set; }
}
```

## <a name="class-csvcolumnmapping"></a>Класс Ксвколумнмаппинг

```csharp
public class CsvColumnMapping
{
    /// The column name (in the Kusto table)
    public string ColumnName { get; set; }

    /// The column's data type in the table (CSL term), if empty, the current column data type will be used.
    /// If column doesn't exist, a new one will be created (alter table) with this data type, if empty, StorageDataType.StringBuffer will be used.
    public string CslDataType { get; set; }

    /// The CSV column dataType (not in use for now)
    public string CsvColumnDataType { get; set; }

    /// CSV ordinal number
    public int Ordinal { get; set; }

    /// This column has a const value (the Ordinal field is ignored, if this value is not null or empty)
    public string ConstValue { get; set; }
}
```

## <a name="enum-datasourceformat"></a>Перечисление Датасаурцеформат

```csharp
public enum DataSourceFormat
{
    csv,        // Data is in a CSV(-comma-separated values) format
    tsv,        // Data is in a TSV(-tab-separated values) format
    scsv,       // Data is in a SCSV(-semicolon-separated values) format
    sohsv,      // Data is in a SOHSV(-SOH (ASCII 1) separated values) format
    psv,        // Data is in a PSV (pipe-separated values) format
    txt,        // Each record is a line and has just one field
    raw,        // The entire stream/file/blob is a single record having a single field
    json,       // Data is in a JSON-line format (each line is record with a single JSON value)
    multijson,  // The data stream is a concatenation of JSON documents (property bags all)
    avro,       // Data is in a AVRO format
    parquet,    // Data is in a Parquet format
}
```


## <a name="example-of-kustoingestionproperties-definition"></a>Пример определения Кустоинжестионпропертиес

```csharp
var guid = new Guid().ToString();
var kustoIngestionProperties = new KustoIngestionProperties("TargetDatabase", "TargetTable")
{
    DropByTags = new List<string> { DateTime.Today.ToString() },
    IngestByTags = new List<string> { guid },
    AdditionalTags = new List<string> { "some tags" },
    IngestIfNotExists = new List<string> { guid },
    CSVMapping = new List<CsvColumnMapping> { new CsvColumnMapping { ColumnName = "columnA", CslDataType = "Dynamic", Ordinal = 1 } },
    JsonMapping = new List<JsonColumnMapping> { new JsonColumnMapping { ColumnName = "columnA" , JsonPath = "$.path" } }, // You can only one of CSV/JSON/AVRO mappings
    AvroMapping = new List<AvroColumnMapping> { new AvroColumnMapping { ColumnName = "columnA" , FieldName = "AvroFieldName" } }, // You can only one of CSV/JSON/AVRO mappings
    ValidationPolicy = new ValidationPolicy { ValidationImplications = ValidationImplications.Fail, ValidationOptions = ValidationOptions.ValidateCsvInputConstantColumns },
    Format = DataSourceFormat.csv
};
```

## <a name="interface-ikustoqueuedingestclient"></a>Икустокуеуединжестклиент интерфейса

Интерфейс Икустокуеуединжестклиент добавляет методы отслеживания, которые следуют за результатом операции приема, и предоставляет RetryPolicy для принимающего клиента.

* пиктопинжестионфаилурес
* жетанддискардтопинжестионфаилурес
* жетанддискардтопинжестионсукцессес

```csharp
public interface IKustoQueuedIngestClient : IKustoIngestClient
{
    /// <summary>
    /// Peeks top (== oldest) ingestion failures  
    /// </summary>
    /// <param name="messagesLimit">Maximum ingestion failures to peek. Default value peeks 32 messages.</param>
    /// <returns>A task which its result contains IEnumerable of <see cref="IngestionFailure"/>. The received messages won't be discarded from the relevant azure queue.</returns>
    Task<IEnumerable<IngestionFailure>> PeekTopIngestionFailures(int messagesLimit = -1);

    /// <summary>
    /// Returns and deletes top (== oldest) ingestion failure notifications 
    /// </summary>
    /// <param name="messagesLimit">Maximum ingestion failure notifications to get. Default value peeks 32 messages.</param>
    /// <returns>A task which its result contains IEnumerable of <see cref="IngestionFailure"/>. The received messages will be discarded from the relevant azure queue.</returns>
    Task<IEnumerable<IngestionFailure>> GetAndDiscardTopIngestionFailures(int messagesLimit = -1);

    /// <summary>
    /// Returns and deletes top (== oldest) ingestion success notifications 
    /// </summary>
    /// <param name="messagesLimit">Maximum ingestion success notifications to get. Default value peeks 32 messages.</param>
    /// <returns>A task which its result contains IEnumerable of <see cref="IngestionSuccess"/>. The received messages will be discarded from the relevant azure queue.</returns>
    Task<IEnumerable<IngestionSuccess>> GetAndDiscardTopIngestionSuccesses(int messagesLimit = -1);

    /// <summary>
    /// An implementation of IRetryPolicy that will be enforced on every ingest call,
    /// which affects how the ingest client handles retrying on transient failures 
    /// </summary>
    IRetryPolicy QueueRetryPolicy { get; set; }
}
```

## <a name="class-kustoqueuedingestionproperties"></a>Класс Кустокуеуединжестионпропертиес

Класс Кустокуеуединжестионпропертиес расширяет Кустоинжестионпропертиес с помощью нескольких контрольных регуляторов, которые можно использовать для точной настройки поведения приема.

|Свойство   |Значение    |
|-----------|-----------|
|флушиммедиатели |По умолчанию имеет значение `false`. Если задано значение `true` , обход механизма агрегирования для службы Управление данными |
|инжестионрепортлевел |Управляет уровнем создания отчетов о состоянии приема (по умолчанию — `FailuresOnly` ). Для обеспечения высокой производительности и использования хранилища рекомендуется не задавать для Инжестионрепортлевел значение.`FailuresAndSuccesses` |
|инжестионрепортмесод |Управляет целевым объектом отчетов о состоянии приема. Доступные варианты: "очередь Azure", "Таблица Azure" или "оба". По умолчанию имеет значение `Queue`.

```csharp
public class KustoQueuedIngestionProperties : KustoIngestionProperties
{
    /// <summary>
    /// Allows to stop the batching phase and will cause to an immediate ingestion.
    /// Defaults to 'false'. 
    /// </summary>
    public bool FlushImmediately { get; set; }

    /// <summary>
    /// Controls the ingestion status report level.
    /// Defaults to 'FailuresOnly'.
    /// </summary>
    public IngestionReportLevel ReportLevel { get; set; }

    /// <summary>
    /// Controls the target of the ingestion status reporting. Available options are Azure Queue, Azure Table, or both.
    /// Defaults to 'Queue'.
    /// </summary>
    public IngestionReportMethod ReportMethod;

    public KustoQueuedIngestionProperties(string databaseName, string tableName);
}
```
