---
title: Прием из хранилища с помощью подписки службы "Сетка событий" Azure обозреватель данных
description: В этой статье описывается прием из хранилища с помощью подписки службы "Сетка событий" в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 88a95ea2fc8e1f417114cfcfd89c4e5003d9bef2
ms.sourcegitcommit: fb54d71660391a63b0c107a9703adea09bfc7cb9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2020
ms.locfileid: "86946110"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Прием данных из хранилища с помощью подписки службы "Сетка событий"

Azure обозреватель данных предлагает непрерывное получение из службы хранилища Azure (хранилища BLOB-объектов и ADLSv2) с помощью подписки службы " [Сетка событий Azure](/azure/event-grid/overview) " для создания уведомлений BLOB и потоковой передачи этих уведомлений в обозреватель данных Azure через концентратор событий.

## <a name="data-format"></a>Формат данных

* Большие двоичные объекты могут быть в любом из [поддерживаемых форматов](../../../ingestion-supported-formats.md).
* Большие двоичные объекты можно сжимать. Дополнительные сведения см. в разделе [Поддерживаемые сжатия](../../../ingestion-supported-formats.md#supported-data-compression-formats).

> [!NOTE]
> В идеале исходный размер несжатых данных должен быть частью метаданных большого двоичного объекта.
> Если размер несжатого файла не указан, Azure обозреватель данных будет оценивать его на основе размера файлов. Можно указать исходный размер данных, задав `rawSizeBytes` для [свойства](#ingestion-properties) в метаданных большого двоичного объекта несжатый размер данных в байтах.
> 
> Существует ограничение размера несжатого приема на файл размером 4 ГБ.

## <a name="ingestion-properties"></a>Свойства приема

[Свойства приема](../../../ingestion-properties.md) большого двоичного объекта можно указать с помощью метаданных большого двоичного объекта.
Задать можно следующие свойства.

|Свойство | Описание|
|---|---|
| равсизебитес | Размер необработанных (несжатых) данных. Для Avro/ORC/Parquet это значение имеет размер до применения сжатия, характерного для формата.|
| кустотабле |  Имя существующей целевой таблицы. Переопределяет `Table` набор в `Data Connection` колонке. |
| кустодатаформат |  Формат данных. Перезаписывает набор **форматов данных** , заданный в колонке **подключения к данным** . |
| кустоинжестионмаппингреференце |  Имя существующего сопоставления приема, которое будет использоваться. Перезаписывает набор **сопоставлений столбцов** в колонке **подключения к данным** .|
| кустоигнорефирстрекорд | Если задано значение `true` , Azure обозреватель данных игнорирует первую строку большого двоичного объекта. Для пропуска заголовков используйте данные в табличном формате (CSV, TSV или аналогичные). |
| кустоекстенттагс | Строка, представляющая [теги](../extents-overview.md#extent-tagging) , которые будут присоединены к результирующему экстенту. |
| кустокреатионтиме |  Переопределяет [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) для большого двоичного объекта в формате строки ISO 8601. Используется для заполнения. |

## <a name="events-routing"></a>Маршрутизация событий

При настройке подключения к хранилищу BLOB-объектов к кластеру Azure обозреватель данных укажите свойства целевой таблицы:
* Имя таблицы
* формат данных
* mapping

Эта настройка является маршрутизацией по умолчанию для данных, которая иногда называется `static routing` .
Можно также указать свойства целевой таблицы для каждого большого двоичного объекта, используя метаданные большого двоичного объекта. Данные будут динамически маршрутизироваться, как указано [свойствами приема](#ingestion-properties).

Ниже приведен пример установки свойств приема в метаданные большого двоичного объекта перед отправкой. Большие двоичные объекты направляются в разные таблицы.

Дополнительные сведения о создании данных см. в разделе [образец кода](#generating-data).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Создание подписки на службу "Сетка событий Azure" в учетной записи хранения

> [!NOTE]
> Для лучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure обозреватель данных.

### <a name="prerequisites"></a>Предварительные требования

* [Создание учетной записи хранения](/azure/storage/common/storage-quickstart-create-account).
  Подписку на уведомления в службе "Сетка событий" можно задать в учетных записях хранения Azure для типа `BlobStorage` или `StorageV2` .
  Также поддерживается включение [Data Lake Storage 2-го поколения](/azure/storage/blobs/data-lake-storage-introduction) .
* [Создайте концентратор событий](/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Подписка на Сетку событий
 
1. Найдите свою учетную запись хранения на портале Azure .
1. В меню слева выберите **события**  >  **Подписка**событий.

     :::image type="content" source="../images/eventgrid/create-event-grid-subscription-1.png" alt-text="Создать подписку на сетку событий":::

1. В окне **Создание подписки на событие** на вкладке **Основные** введите следующие значения:

    :::image type="content" source="../images/eventgrid/create-event-grid-subscription-2.png" alt-text="Создание значений подписки на события для ввода":::

    |**Параметр** | **Рекомендуемое значение** | **Описание поля**|
    |---|---|---|
    | Имя | *test-grid-connection* | Имя подписки на сетку событий, которую необходимо создать.|
    | Схема событий | *Схема сетки событий* | Схема, которая должна использоваться для сетки событий. |
    | Тип раздела | *Учетная запись хранения* | Тип раздела сетки событий. |
    | Исходный ресурс | *gridteststorage1* | Имя учетной записи хранения. |
    | Имя системного раздела | *gridteststorage1...* | Системный раздел, в котором служба хранилища Azure публикует события. Этот системный раздел затем перенаправляет событие подписчику, который получает и обрабатывает события. |
    | Фильтрация по типам событий | *BLOB-объект создан* | Позволяет указать, о каких конкретных событиях нужно получать уведомления. Текущий поддерживаемый тип — Microsoft. Storage. BlobCreated. Не забудьте выбрать его при создании подписки.|
    | Тип конечной точки | *Центры событий* | Тип конечной точки, куда будут отправляться события. |
    | Конечная точка | *test-hub* | Созданный концентратор событий. |

1. Выберите вкладку **фильтры** , если хотите отвести трассировку по конкретным темам. Настройте фильтры для получения уведомлений следующим образом:
   * Выберите **Включить фильтрацию тем**
   * **Тема начинается с** Field — это префикс *литерала* субъекта. Так как применяется шаблон *StartsWith*, он может охватывать несколько контейнеров, папок или больших двоичных объектов. Подстановочные знаки не допускаются.
       * Чтобы определить фильтр для контейнера больших двоичных объектов, *необходимо* установить поле следующим образом: *`/blobServices/default/containers/[container prefix]`* .
       * Чтобы определить фильтр для префикса большого двоичного объекта (или папки в Azure Data Lake Gen2), поле *должно* быть установлено следующим образом: *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
   * Поле **Тема заканчивается на** — это суффикс *литерала* большого двоичного объекта. Подстановочные знаки не допускаются.
   * Поле **сопоставления темы с учетом регистра** указывает, учитывается ли регистр в фильтрах префиксов и суффиксов.
   * Дополнительные сведения о фильтрации событий см. в разделе [события хранилища BLOB-объектов](/azure/storage/blobs/storage-blob-event-overview#filtering-events) .
    
        :::image type="content" source="../images/eventgrid/filters-tab.png" alt-text="Сетка событий вкладки "фильтры"":::

> [!NOTE]
> Если конечная точка не подтверждает получение события, служба "Сетка событий Azure" активирует механизм повтора. В случае сбоя доставки повторных попыток сетка событий доставляет недоставленные события в учетную запись хранения, используя процесс *недоставленных сообщений*. Дополнительные сведения см. в разделе [Доставка и повторные попытки доставки сообщений сетки событий](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration).

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение приема данных к Azure обозреватель данных

* С помощью портал Azure: [Создайте подключение к данным в службе "Сетка событий" в обозреватель данных Azure](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Использование Kusto Management .NET SDK: [Добавление подключения к данным в сетке событий](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Использование Kusto Management Python SDK: [Добавление подключения к данным в сетке событий](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* С помощью [шаблона Azure Resource Manager для добавления подключения к данным в сетке событий](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Создание данных

> [!NOTE]
> * Используется `BlockBlob` для создания данных. Функция `AppendBlob` не поддерживается.

Ниже приведен пример создания большого двоичного объекта из локального файла, установки свойств приема в метаданные большого двоичного объекта и его передачи:

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob's metadata & uploading the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> Для использования пакета SDK для хранилища Azure Data Lake Gen2 требуется использовать `CreateFile` для отправки файлов и `Flush` в конце, если для параметра Close задано значение true.
> Подробный пример правильного использования пакета SDK Data Lake Gen2 см. в разделе [Отправка файла с помощью пакета SDK для Azure Data Lake](../../../data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).

## <a name="blob-lifecycle"></a>Жизненный цикл BLOB-объектов

Обозреватель данных Azure не удаляет большие двоичные объекты после приема. Используйте [жизненный цикл хранилища BLOB-объектов Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением больших двоичных объектов. Рекомендуется хранить большие двоичные объекты в течение трех – пять дней.

## <a name="known-issues"></a>Известные проблемы

* При использовании обозреватель данных Azure для [экспорта](../data-export/export-data-to-storage.md) файлов, используемых для приема в сетке событий, обратите внимание на следующее. 
    * Уведомления в службе "Сетка событий" не вызываются, если строка подключения, предоставленная для команды экспорта, или строка подключения, предоставленная [внешней таблице](../data-export/export-data-to-an-external-table.md) , является связывающей строкой в [ADLS 2-го поколения формате](../../api/connection-strings/storage.md#azure-data-lake-store) (например, `abfss://filesystem@accountname.dfs.core.windows.net` ), но учетная запись хранения не включена для иерархического пространства имен. 
    * Если учетная запись не включена для иерархического пространства имен, в строке подключения должен использоваться формат [хранилища BLOB-объектов](../../api/connection-strings/storage.md#azure-storage-blob) (например, `https://accountname.blob.core.windows.net` ). Экспорт работает должным образом даже при использовании строки подключения ADLS 2-го поколения, но уведомления не будут активированы, и прием сетки событий не будет работать.
