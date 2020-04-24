---
title: Глотать из хранилища с помощью подписки Event Grid - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается ingest от хранения с помощью подписки Event Grid в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 682c39b11292c7e198dba46dad9b5bfa3b520c0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521528"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Глотать из хранилища с помощью подписки Event Grid

Azure Data Explorer предлагает непрерывную проглатку из хранилища Azure (Blob storage и ADLSv2) с подпиской [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) на созданные капли уведомления и потоковое воспроизведение этих уведомлений в Kusto через концентратор событий.

## <a name="data-format"></a>Формат данных

* Blobs может быть в любом из [поддерживаемых форматов.](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)
* Капли могут быть сжаты в любом из [поддерживаемых сжатий](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats#supported-data-compression-formats)

> [!NOTE]
> В идеале исходный несжатый размер данных должен быть частью метаданных капли.
> Если несжатый размер не указан, Azure Data Explorer оценит его на основе размера файла. Предоставьте исходный размер `rawSizeBytes` данных, установив [свойство](#ingestion-properties) метаданных blob на **несжатый** размер данных в байтах.
> Обратите внимание, что на файл в 4 ГБ имеется ограничение несжатого размера.

## <a name="ingestion-properties"></a>Свойства приема

Вы можете указать [свойства проглатывания](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) капли через метаданные капли.
Задать можно следующие свойства.

|Свойство | Описание|
|---|---|
| rawSizeBytes | Размер необработанных (несжатых) данных. Для Avro/ORC/Parquet это размер до применения сжатия формата.|
| kustoTable |  Название существующей целевой таблицы. Переопределяет `Table` набор на `Data Connection` лезвии. |
| kustoDataФормат |  Формат данных. Переопределяет `Data format` набор на `Data Connection` лезвии. |
| kustoIngestionMappingСправка |  Название существующего отображения приема, которое будет использоваться. Переопределяет `Column mapping` набор на `Data Connection` лезвии.|
| kustoIgnoreFirstRecord | При установке `true`на Azure Data Explorer игнорируется первый ряд капли. Используйте данные табликового формата (CSV, TSV или аналогичный) для игнорирования заголовков. |
| kustoExtentTags | Строка, представляющая [теги,](../extents-overview.md#extent-tagging) которые будут прикреплены в результирующей степени. |
| kustoCreationTime |  Оверлет$IngestionTime для капли, отформатированный как строка ISO 8601. [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) Используйте для заправки. |

## <a name="events-routing"></a>Разгром событий

При настройке подключения к хранилищу кабы в кластере Azure Data Explorer укажите свойства целевой таблицы (имя таблицы, формат данных и отображение). Это по умолчанию разгром для ваших данных, `static routig`также называется как .
Вы также можете указать свойства целевой таблицы для каждого капли, используя метаданные blob. Данные будут динамически маршрутизируются в зависимости от [свойств приема.](#ingestion-properties)

Ниже приводится пример для установки свойств приема в метаданные капли перед загрузкой. Капли направляются в разные таблицы.

Пожалуйста, обратитесь к [примеру кода](#generating-data) для получения более подробной информации о том, как генерировать данные.

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
> Для наилучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure Data Explorer.

### <a name="prerequisites"></a>Предварительные требования

* [Создание учетной записи хранения](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
  Подписка на уведомление Event Grid может быть `BlobStorage` `StorageV2`установлена на учетных записях хранения Azure в натуральном виде или в . 
  Также поддерживается включение [данных хранилища озер Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)
* [Создание концентратора событий.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)

### <a name="event-grid-subscription"></a>Подписка на Сетку событий

* Kusto выбран `Event Hub` в качестве типа endpoind, используемого для транспортировки уведомлений о событиях хранения кабл. `Event Grid schema`является выбранной схемой для уведомлений. Обратите внимание, что каждый Even Hub может обслуживать одно соединение.
* Соединение подписки на хранение капли обрабатывает `Microsoft.Storage.BlobCreated`уведомления типа. Убедитесь в том, чтобы выбрать его при создании подписки. Обратите внимание, что другие типы уведомлений, если они выбраны, игнорируются.
* Одна подписка может уведомить о событиях хранения в одном контейнере или более. Если вы хотите отслеживать файлы из определенного контейнера (ы), установите фильтры для уведомлений следующим образом: При настройке соединения, позаботьтесь о следующих значениях: 
   * **Тема начинается с** фильтра является *буквальной* префиксом контейнера капли. Так как применен шаблон *startswith*, он может охватывать несколько контейнеров. Подстановочные знаки не допускаются.
     Она *должна* быть установлена *`/blobServices/default/containers/<prefix>`* следующим образом: . Например: */blobServices/default/containers/StormEvents-2020-*
   * Поле **Тема заканчивается на** — это суффикс *литерала* большого двоичного объекта. Подстановочные знаки не допускаются. Полезно для фильтрации расширений файлов.

Подробный пошаговой можно найти в [подписке «Создание сетки событий» в руководстве по учетной записи хранилища.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-subscription-in-your-storage-account)

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение к подключению к данным в Azure Data Explorer

* Через портал Azure: [Создание подключения к данным Event Grid в Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-data-connection-in-azure-data-explorer)
* Использование управления Kusto .NET SDK: [Добавить подключение к данным Event Grid](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-csharp#add-an-event-grid-data-connection)
* Использование управления Kusto Python SDK: [Добавить подключение к данным Event Grid](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-python#add-an-event-grid-data-connection)
* С шаблоном ARM: [шаблон менеджера ресурсов Azure для добавления подключения данных Event Grid](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-resource-manager#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Генерация данных

> [!NOTE]
> * Используется `BlockBlob` для генерации данных. Функция `AppendBlob` не поддерживается.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

Ниже приводится пример для создания капли из локального файла, настройки свойств приема в метаданные капли и загрузки:

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

## <a name="blob-lifecycle"></a>Цикл жизни Blob

Azure Data Explorer не будет удалять проглатывание пятки, но сохранит их в течение трех-пяти дней. Используйте [жизненный цикл хранения Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением капли.