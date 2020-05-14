---
title: Kusto. прием сообщений о состоянии приема — Azure обозреватель данных
description: В этой статье описывается Kusto. прием отчетов о состоянии приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 76ae07e2e7bdbb15900385b1e2feab0c9ff97d01
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373635"
---
# <a name="kustoingest-ingestion-status-reporting"></a>Kusto. прием отчетов о состоянии приема

В этой статье объясняется, как использовать функции [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) для наблюдения за состоянием запроса приема.

## <a name="description-classes"></a>Классы описания

Эти классы описания содержат важные сведения о принимаемых источниках данных и должны использоваться в операции приема. 

* SourceDescription
* датареадердескриптион
* стреамдескриптион
* FileDescription
* блобдескриптион

Все классы являются производными от абстрактного класса `SourceDescription` и используются для создания экземпляра уникального идентификатора для каждого источника данных. Каждый идентификатор будет использоваться для отслеживания состояния и будет отображаться во всех отчетах, трассировках и исключениях, связанных с соответствующей операцией.

### <a name="class-sourcedescription"></a>Класс Саурцедескриптион

Большие наборы данных будут разбиты на фрагменты размером 1 ГБ, и каждая часть будет принята отдельно. Один и тот же SourceId будет применяться ко всем операциям приема, созданным из одного и того же набора данных.   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>Класс Датареадердескриптион

```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>Класс Стреамдескриптион

```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>Класс Филедескриптион

```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>Класс Блобдескриптион

```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>Представление результатов приема

### <a name="interface-ikustoingestionresult"></a>Икустоинжестионресулт интерфейса

Этот интерфейс фиксирует результат одной операции приема в очереди и может быть извлечен `SourceId` .

```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>Класс Инжестионстатус

Инжестионстатус содержит полное состояние одной операции приема.

```csharp
public class IngestionStatus
{
    // The ingestion status returns from the service. Status remains 'Pending' during the ingestion process and
    // is updated by the service once the ingestion completes. When <see cref="IngestionReportMethod"/> is set to 'Queue' the ingestion status
    // will always be 'Queued' and the caller needs to query the report queues for ingestion status, as configured. To query statuses that were
    // reported to queue, see: <see href="https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-status#ingestion-status-in-azure-queue"/>.
    // When <see cref="IngestionReportMethod"/> is set to 'Table', call <see cref="IKustoIngestionResult.GetIngestionStatusBySourceId"/> or
    // <see cref="IKustoIngestionResult.GetIngestionStatusCollection"/> to retrieve the most recent ingestion status.
    public Status Status { get; set; }
    // A unique identifier representing the ingested source. Can be supplied during the ingestion execution.
    public Guid IngestionSourceId { get; set; }
    // The URI of the blob, potentially including the secret needed to access the blob.
    // This can be a filesystem URI (on-premises deployments only), or an Azure Blob Storage URI (including a SAS key or a semicolon followed by the account key)
    public string IngestionSourcePath { get; set; }
    // The name of the database holding the target table.
    public string Database { get; set; }
    // The name of the target table into which the data will be ingested.
    public string Table { get; set; }
    // The last updated time of the ingestion status.
    public DateTime UpdatedOn { get; set; }
    // The ingestion's operation Id.
    public Guid OperationId { get; set; }
    // The ingestion operation activity Id.
    public Guid ActivityId { get; set; }
    // In case of a failure - indicates the failure's error code.
    public IngestionErrorCode ErrorCode { get; set; }
    // In case of a failure - indicates the failure's status.
    public FailureStatus FailureStatus { get; set; }
    // In case of a failure - indicates the failure's details.
    public string Details { get; set; }
    // In case of a failure - indicates whether or not the failures originate from an Update Policy.
    public bool OriginatesFromUpdatePolicy { get; set; }
}
```

### <a name="status-enumeration"></a>Перечисление состояния

|Значение              |Значение                                                                                     |Временный или постоянный
|-------------------|-----------------------------------------------------------------------------------------------------|---------|
|Pending            |Значение может измениться во время приема, в зависимости от результата операции приема. |Временные таблицы|
|Успешно          |Данные успешно приняты                                                              |Постоянно| 
|Ошибка             |Сбой приема                                                                                     |Постоянно|
|Поставлено в очередь             |Данные были помещены в очередь для приема                                                               |Постоянно|
|Пропущено            |Данные не были предоставлены, и операция приема пропущена.                                            |Постоянно|
|Частично |Часть данных успешно принята, в то время как произошел сбой                                        |Постоянно|

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>Отслеживание состояния приема (Кустокуеуединжестклиент)

[Икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) — это клиент "пожар-and-забыть". Операция приема на стороне клиента завершается путем отправки сообщения в очередь Azure. После отправки клиентское задание выполняется. Для удобства пользователя Кустокуеуединжестклиент предоставляет механизм отслеживания отдельного состояния приема. Этот механизм не предназначен для массового использования в конвейерах приема с высокой пропускной способностью. Этот механизм предназначен для приема точности, когда скорость относительно низкая, а требования к отслеживанию являются ограниченными.

> [!WARNING]
> Следует избегать включения положительных уведомлений для каждого запроса на прием потоков данных большого объема, поскольку это повлечет за собой экстремальную нагрузку на базовые ресурсы xStore, что может привести к увеличению задержки приема и даже полному реагированию на кластер.

Следующие свойства (задаются в [кустокуеуединжестионпропертиес](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)) управляют уровнем и транспортом для уведомлений об успешном приеме приема или сбоях.

### <a name="ingestionreportlevel-enumeration"></a>Перечисление Инжестионрепортлевел

```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>Перечисление Инжестионрепортмесод

```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

Чтобы отвести состояние приема, укажите следующие данные в [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) , с помощью которых вы выполняете операцию приема:
1.  Задайте `IngestionReportLevel` для свойства требуемый уровень отчета. Либо `FailuresOnly` (это значение по умолчанию), либо `FailuresAndSuccesses` .
Если задано значение `None` , в конце приема ничего не отображается.
1.  Укажите `IngestionReportMethod`  -  `Queue` , `Table` или `both` .

Пример использования можно найти на странице с [примерами Kusto. приема](kusto-ingest-client-examples.md) .

### <a name="ingestion-status-in-the-azure-table"></a>Состояние приема в таблице Azure

`IKustoIngestionResult`Интерфейс, возвращаемый каждой операцией приема, содержит функции, которые можно использовать для запроса состояния приема.
Обратите особое внимание на `Status` свойство возвращаемых `IngestionStatus` объектов:
* `Pending`Указывает, что источник был помещен в очередь на прием и еще не обновлен. Использование функции для запроса состояния источника
* `Succeeded`Указывает, что источник успешно принят
* `Failed`Указывает, что источник не удалось принять

> [!NOTE]
> Получение `Queued` состояния указывает, что `IngestionReportMethod` был оставлен по значению "Queue" по умолчанию. Это постоянное состояние и повторное вызов `GetIngestionStatusBySourceId` `GetIngestionStatusCollection` функций или, всегда приведет к тому же состоянию "в очереди".
> Чтобы проверить состояние приема в таблице Azure до приема, убедитесь, что для `IngestionReportMethod` свойства [кустокуеуединжестионпропертиес](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) задано значение `Table` . Если вы также хотите, чтобы состояние приема было передаваться в очередь, установите для параметра status значение `QueueAndTable` .

### <a name="ingestion-status-in-azure-queue"></a>Состояние приема в очереди Azure

Эти `IKustoIngestionResult` методы применимы только для проверки состояния в таблице Azure. Чтобы запросить состояния, отправленные в очередь Azure, используйте следующие методы [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) .

|Метод                                  |Назначение     |
|----------------------------------------|------------|
|пиктопинжестионфаилурес                |Асинхронный метод, возвращающий сведения о самых ранних сбоях приема, которые еще не были отклонены из-за ограничения запрошенных сообщений. |
|жетанддискардтопинжестионфаилурес       |Асинхронный метод, возвращающий и удаляющий самые ранние сбои приема, которые еще не были отклонены из-за ограничения запрошенных сообщений |
|жетанддискардтопинжестионсукцессес      |Асинхронный метод, возвращающий и удаляющий наиболее успешно выполненные операции приема, которые еще не были отклонены из-за ограничения запрошенных сообщений. Этот метод имеет смысл, только если `IngestionReportLevel` для задано значение`FailuresAndSuccesses` |

### <a name="ingestion-failures-retrieved-from-the-azure-queue"></a>Ошибки приема, полученные из очереди Azure

Ошибки приема представлены `IngestionFailure` объектом, содержащим полезную информацию о сбое.

|Свойство.                      |Значение     |
|------------------------------|------------|
|Таблица & базы данных              |Целевые имена базы данных и таблицы |
|инжестионсаурцепас           |Путь к полученному большому двоичному объекту. Будет содержать исходное имя файла при приеме файла. Будет случайным при приеме DataReader |
|фаилурестатус                 |`Permanent`(повторная попытка не будет выполнена), `Transient` (повторная попытка будет выполнена) или `Exhausted` (несколько повторных попыток также не удалось выполнить). |
|RootActivityId &  |ИДЕНТИФИКАТОР операции и идентификатор свойство rootactivity приема (полезно для дальнейшего устранения неполадок) |
|фаиледон                      |Время сбоя в формате UTC. Будет превышать время вызова метода приема, так как перед выполнением приема данные были агрегированы |
|Сведения                       |Другие сведения о сбое (если таковые имеются) |
|ErrorCode                     |`IngestionErrorCode`Перечисление представляет код ошибки приема, если произошел сбой |
