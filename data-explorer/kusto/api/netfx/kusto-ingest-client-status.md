---
title: Kusto.Ingest Справка - Отчет о состоянии приема - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается ссылка Kusto.Ingest - Отчетность о состоянии приема в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1a3eed1db0ec7d3dd4bc83c0a65f342020b2a387
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523721"
---
# <a name="kustoingest-reference---ingestion-status-reporting"></a>Kusto.Ingest Справка - Отчет о состоянии приема
В этой статье объясняется, как использовать возможности [IKusto'ueingIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) для отслеживания состояния запроса на проглатку.

## <a name="sourcedescription-datareaderdescription-streamdescription-filedescription-and-blobdescription"></a>SourceDescription, DataReaderОписание, StreamDescription, FileDescription и BlobDescription
Эти различные классы описания инкапсулируют важные детали относительно исходных данных, которые должны быть проглатывается, и могут и даже должны быть предоставлены для операции по поглажению.
Все эти классы являются производными от абстрактного класса `SourceDescription` и используются для мгновенного получения уникального идентификатора для каждого источника данных.
Предоставленный идентификатор предназначен для последующего отслеживания состояния операции и будет отображаться во всех отчетах, следах и исключениях, связанных с соответствующей операцией.

### <a name="class-sourcedescription"></a>Источник классаОписание
>При глотании большого набора данных (например, DataReader более 1 ГБ) - данные будут разделены на 1 ГБ кусков и попадает отдельно.<BR>В этом случае тот же SourceId будет применяться ко всем операциям, полученным из одного и того же набора данных.   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>Класс ДанныхReaderОписание
```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>Класс потокОписание
```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>Файл классаОписание
```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>Класс BlobОписание
```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>Представление результатов приема

### <a name="interface-ikustoingestionresult"></a>Интерфейс IKustoIngestionResult
Этот интерфейс фиксирует результат одной операции по проглатыванию в очереди и позволяет ее извлечение. `SourceId`
```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>Класс IngestionStatus
IngestionStatus инкапсулирует полный статус одной операции приема.
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

### <a name="status-enumeration"></a>Перечисление статуса
|Значение |Значение |
|------------|------------|
|Ожидает |Временные. Может измениться в ходе приема на основе результатов операции по проглатывания |
|Выполнено |Постоянного. он данные были успешно попадает |
|Ошибка |Постоянного. Загласка не удалось |
|Поставлено в очередь |Постоянного. Данные были поставлены в очередь для приема |
|Пропущено |Постоянного. Данные не были предоставлены, и операция по проглочению была пропущена |
|ЧастичноSucceeded |Постоянного. Часть данных была успешно проглочена, в то время как некоторые не |

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>Отслеживание статуса приема (КустоОчередьдингеСКлиент)
[IKusto'u'uingingclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) является клиентом "пожар-и-забыть" - операция по заглажению на стороне клиента заканчивается, разместив сообщение в очередь Azure, после чего работа клиента будет выполнена. Для удобства клиента, Kusto'uedIngestClient предоставляет механизм для отслеживания индивидуального состояния приема. Это не предназначено для массового использования на трубопроводах с высокой пропускной приглажением, а скорее для «точного» приема, когда скорость относительно низка явка и требования к отслеживанию очень строгие.

> [!WARNING]
> Следует избегать включения положительных уведомлений для каждого запроса на использование больших потоков данных, так как это создает чрезвычайную нагрузку на базовые ресурсы xStore, > что может привести к увеличению задержки приема и даже полной невосприимчивости кластера.


Следующие свойства (набор на [Kusto'UudIngestionPropertiesPropertiesProperties)](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)контролируют уровень и транспорт для уведомлений об успешном проглатывании или отказе:

### <a name="ingestionreportlevel-enumeration"></a>Перечисление IngestionReportLevel
```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>Перечисление IngestionReportMethod
```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

Чтобы иметь возможность отслеживать состояние вашего приема, убедитесь, что вы предоставляете следующее [iKusto'ueingIngclient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) вы выполняете глоток операции с:
1.  Установите `IngestionReportLevel`свойство на желаемый уровень отчета - либо Отказытолько (что является значением по умолчанию) или ОтказыИИУспехи.
При установке на Нет, ничего не будет сообщено в конце приема.
2.  Укажите `IngestionReportMethod` желаемое - Очередь, таблица или и то, и другое.

Пример использования можно найти на странице [Kusto.Ingest Examples.](kusto-ingest-client-examples.md)

### <a name="ingestion-status-in-azure-table"></a>Статус проглатывания в таблице Azure
Интерфейс, `IKustoIngestionResult` который возвращается из каждой операции глотать содержит функции, которые могут быть использованы для запроса статуса приема.
Обратите особое `Status` внимание на `IngestionStatus` имущество возвращенных объектов:
* `Pending`указывает на то, что источник был поставлен в очередь для приема и еще не обновлен; использовать функцию снова для запроса состояния источника
* `Succeeded`указывает на то, что источник был успешно проглочен
* `Failed`указывает на то, что источник не был проглочен

>Обратите внимание, `Queued` что получение `IngestionReportMethod` статуса указывает на то, что он был оставлен по умолчанию значение "Очередь". Это постоянный статус и повторное ссылаясь на GetIngestionStatusBySourceId или GetIngestionStatusCollectionвсегдая функции всегда приведет к тому же "Очередь" статус.<BR>Чтобы иметь возможность проверить состояние проглатывания в таблице Azure, пожалуйста, `IngestionReportMethod` проверьте до приема, что свойство [Kusto-ОчередьIngingestionPropertiesProperties установлен](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) `Table` (или `QueueAndTable` если вы также хотите, чтобы статус приема был сообщен в очередь).

### <a name="ingestion-status-in-azure-queue"></a>Статус приема в очереди Azure
Методы `IKustoIngestionResult` имеют значение только для проверки состояния в таблице Azure. Для запроса статусов, которые были сообщены в очередь Azure, используйте следующие методы [IKusto'ueingIngestClient:](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)

|Метод |Назначение |
|------------|------------|
|PeekTopIngestionFailures |Метод Async, который возвращает информацию о ранних сбоях приема, которые не были отброшены, в соответствии с запрошенным пределом сообщений |
|GetAnddiscardtopIngestionОтказ |Метод Async, который возвращает и отбрасывает ранние сбои приема, которые не были отброшены, в соответствии с запрошенным пределом сообщений |
|GetAnddiscardTopIngestionУспехы |Метод Async, который возвращает и отбрасывает самые ранние успехи приема, которые не были отброшены, в соответствии с запрошенным пределом сообщений (только релевантным, если `IngestionReportLevel` установлен о`FailuresAndSuccesses` |


### <a name="ingestion-failures-retrieved-from-azure-queue"></a>Сбои приема, извлеченные из очереди Azure
Сбои приема представлены объектом, `IngestionFailure` содержащим полезную информацию о сбое:

|Свойство |Значение |
|------------|------------|
|Таблица & базы данных |Предполагаемые имена баз данных и таблиц |
|ПроглатываниеИсточникPath |Путь проглоченной капли. Будет содержать исходное имя файла в случае проглатывания файла. Будет случайным в случае приема DataReader |
|ОтказСтатус |`Permanent`(не будет выполнена повторная попытка), `Transient` (повторная `Exhausted` попытка будет выполнена), или (несколько повторов также не удалось) |
|OperationId & RootActivityId |Идентификатор операции и идентификатор RootActivity при проглатывании (полезно для дальнейшего устранения неполадок) |
|Неудача |ВРЕМЯ сбоя UTC. Будет больше, чем время, когда был вызван метод приема, так как данные агрегируются перед выполнением |
|Сведения |Другие подробности, касающиеся сбоя (если таковые имеются) |
|ErrorCode |`IngestionErrorCode`перечисление, представляющее код ошибки при впонировании, в случае сбоя|