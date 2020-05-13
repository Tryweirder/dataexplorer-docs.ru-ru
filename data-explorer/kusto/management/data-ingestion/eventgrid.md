---
title: Прием из хранилища с помощью подписки службы "Сетка событий" в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается прием из хранилища с помощью подписки службы "Сетка событий" в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 7673b50a8d1ff401f8c2fa086b7eec34c0517238
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373483"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Прием данных из хранилища с помощью подписки службы "Сетка событий"

Azure обозреватель данных предлагает непрерывное получение из службы хранилища Azure (хранилища BLOB-объектов и ADLSv2) с помощью подписки службы " [Сетка событий Azure](https://docs.microsoft.com/azure/event-grid/overview) " для создания уведомлений BLOB и потоковой передачи этих уведомлений в Kusto через концентратор событий.

## <a name="data-format"></a>Формат данных

* Большие двоичные объекты могут быть в любом из [поддерживаемых форматов](../../../ingestion-supported-formats.md).
* Большие двоичные объекты можно сжимать в любом из [поддерживаемых сжатий](../../../ingestion-supported-formats.md#supported-data-compression-formats)

> [!NOTE]
> В идеале исходный размер несжатых данных должен быть частью метаданных большого двоичного объекта.
> Если размер без сжатия не указан, Azure обозреватель данных будет оценивать его на основе размера файла. Укажите исходный размер данных, задав для `rawSizeBytes` [свойства](#ingestion-properties) метаданных большого двоичного объекта значение **несжатого** размера данных в байтах.
> Обратите внимание на ограничение размера несжатого приема на файл 4 ГБ.

## <a name="ingestion-properties"></a>Свойства приема

[Свойства приема](../../../ingestion-properties.md) большого двоичного объекта можно указать с помощью метаданных большого двоичного объекта.
Задать можно следующие свойства.

|Свойство. | Описание|
|---|---|
| равсизебитес | Размер необработанных (несжатых) данных. Для Avro/ORC/Parquet это размер до применения сжатия, характерного для формата.|
| кустотабле |  Имя существующей целевой таблицы. Переопределяет `Table` набор в `Data Connection` колонке. |
| кустодатаформат |  Формат данных. Переопределяет `Data format` набор в `Data Connection` колонке. |
| кустоинжестионмаппингреференце |  Имя существующего сопоставления приема, которое будет использоваться. Переопределяет `Column mapping` набор в `Data Connection` колонке.|
| кустоигнорефирстрекорд | Если задано значение `true` , Azure обозреватель данных игнорирует первую строку большого двоичного объекта. Для пропуска заголовков используйте данные в табличном формате (CSV, TSV или аналогичные). |
| кустоекстенттагс | Строка, представляющая [теги](../extents-overview.md#extent-tagging) , которые будут присоединены к результирующему экстенту. |
| кустокреатионтиме |  Переопределяет [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) для большого двоичного объекта в формате строки ISO 8601. Используется для заполнения. |

## <a name="events-routing"></a>Маршрутизация событий

При настройке подключения к хранилищу BLOB-объектов к кластеру Azure обозреватель данных укажите свойства целевой таблицы (имя таблицы, формат данных и сопоставление). Это маршрутизация по умолчанию для данных, которую также называют `static routig` .
Можно также указать свойства целевой таблицы для каждого большого двоичного объекта, используя метаданные большого двоичного объекта. Данные будут пересылаться динамически, как указано [свойствами приема](#ingestion-properties).

Ниже приведен пример установки свойств приема в метаданные большого двоичного объекта перед отправкой. Большие двоичные объекты направляются в разные таблицы.

Дополнительные сведения о создании данных см. в [примере кода](#generating-data) .

 ```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-event-grid-subscription"></a>Создание подписки для службы "Сетка событий"

> [!Note]
> Для лучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure обозреватель данных.

### <a name="prerequisites"></a>Предварительные требования

* [Создание учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
  Подписку на уведомления в службе "Сетка событий" можно настроить для учетных записей хранения Azure типа `BlobStorage` или `StorageV2` . 
  Также поддерживается включение [Data Lake Storage 2-го поколения](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) .
* [Создайте концентратор событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Подписка на Сетку событий

* Kusto выбрано `Event Hub` как тип ендпоинд, используемый для перетранспортировки уведомлений о событиях хранилища BLOB-объектов. `Event Grid schema`Выбранная схема для уведомлений. Обратите внимание, что каждый четный концентратор может обслуживать одно подключение.
* Подключение к подписке хранилища BLOB-объектов обрабатывает уведомления типа `Microsoft.Storage.BlobCreated` . Не забудьте выбрать его при создании подписки. Обратите внимание, что другие типы уведомлений, если они выбраны, игнорируются.
* Одна подписка может уведомлять о событиях хранилища в одном контейнере или более. Если вы хотите отформатировать файлы из определенного контейнера, настройте фильтры для уведомлений следующим образом: при настройке подключения примите спеЦиел внимание на следующие значения: 
   * **Тема начинается с** Filter — это *литеральный* префикс контейнера больших двоичных объектов. Так как применен шаблон *startswith*, он может охватывать несколько контейнеров. Подстановочные знаки не допускаются.
     Его *необходимо* задать следующим образом: *`/blobServices/default/containers/<prefix>`* . Например: */blobServices/Default/Containers/StormEvents-2020-*
   * Поле **Тема заканчивается на** — это суффикс *литерала* большого двоичного объекта. Подстановочные знаки не допускаются. Полезно для фильтрации расширений файлов.

Подробное пошаговое руководство можно найти в разделе как [создать подписку на сетку событий в руководстве по учетной записи хранения](../../../ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account) .

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение приема данных к Azure обозреватель данных

* С помощью портала Azure [Создайте подключение к данным в службе "Сетка событий" в Azure обозреватель данных](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Использование Kusto Management .NET SDK: [Добавление подключения к данным в сетке событий](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Использование Kusto Management Python SDK: [Добавление подключения к данным в сетке событий](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* С помощью шаблона ARM: [Azure Resource Manager шаблон для добавления подключения к данным сетки событий](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection) .

### <a name="generating-data"></a>Создание данных

> [!NOTE]
> * Используется `BlockBlob` для создания данных. Функция `AppendBlob` не поддерживается.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

Ниже приведен пример создания большого двоичного объекта из локального файла, установки свойств приема в метаданные большого двоичного объекта и передачи.

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

## <a name="blob-lifecycle"></a>Жизненный цикл BLOB-объектов

Azure обозреватель данных не удалит прием BLOB-объектов, но будет хранить их в течение трех – 5 дней. Используйте [жизненный цикл хранилища BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением больших двоичных объектов.