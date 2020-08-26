---
title: Прием из хранилища с помощью подписки службы "Сетка событий" Azure обозреватель данных
description: В этой статье описывается прием из хранилища с помощью подписки службы "Сетка событий" в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 5369f3166e3700740be2a30927da793a8d21e05c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874908"
---
# <a name="connect-to-event-grid"></a>Подключение к Сетке событий

Сетка событий — это конвейер, который прослушивает службу хранилища Azure и обновляет обозреватель данных Azure для извлечения сведений при возникновении событий подписки. Azure обозреватель данных предлагает непрерывное получение из службы хранилища Azure (хранилища BLOB-объектов и ADLSv2) с помощью подписки службы " [Сетка событий Azure](/azure/event-grid/overview) " для создания уведомлений BLOB и потоковой передачи этих уведомлений в обозреватель данных Azure через концентратор событий.

Конвейер приема в сетке событий проходит через несколько шагов. Вы создадите целевую таблицу и в Azure обозреватель данных, в которую будут поступать [данные в определенном формате](#data-format) . Затем вы создадите подключение к данным в службе "Сетка событий" в Azure обозреватель данных. Для подключения к данным в сетке событий необходимо указать сведения о [маршрутизации событий](#set-events-routing) , например таблицу для отправки данных и сопоставление таблиц. Вы также указываете [Свойства приема](#set-ingestion-properties), которые описывают данные для приема, целевую таблицу и сопоставление. Этим процессом можно управлять с помощью [портал Azure](ingest-data-event-grid.md), программно с помощью [C#](data-connection-event-grid-csharp.md) или [Python](data-connection-event-grid-python.md)или с помощью [шаблона Azure Resource Manager](data-connection-event-grid-resource-manager.md).

## <a name="data-format"></a>Формат данных

* См. раздел [Поддерживаемые форматы](ingestion-supported-formats.md).
* См. раздел [Поддерживаемые сжатия](ingestion-supported-formats.md#supported-data-compression-formats).
  * Исходный размер несжатых данных должен быть частью метаданных большого двоичного объекта, иначе Azure обозреватель данных выполнит оценку.  Ограничение размера несжатого приема на файл составляет 4 ГБ.
 
## <a name="set-ingestion-properties"></a>Задание свойств приема

[Свойства приема](ingestion-properties.md) большого двоичного объекта можно указать с помощью метаданных большого двоичного объекта.
Задать можно следующие свойства.

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="set-events-routing"></a>Настройка маршрутизации событий

При настройке подключения к хранилищу BLOB-объектов к кластеру Azure обозреватель данных укажите свойства целевой таблицы:
* Имя таблицы
* формат данных
* mapping

Эта настройка является маршрутизацией по умолчанию для данных, которая иногда называется `static routing` .
Можно также указать свойства целевой таблицы для каждого большого двоичного объекта, используя метаданные большого двоичного объекта. Данные будут динамически маршрутизироваться, как указано [свойствами приема](#set-ingestion-properties).

В следующем примере показано, как задать свойства приема для метаданных большого двоичного объекта перед их отправкой. Большие двоичные объекты направляются в разные таблицы.

Дополнительные сведения см. в разделе [Создание данных](#generate-data).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

### <a name="generate-data"></a>Создание данных

> [!NOTE]
> Используется `BlockBlob` для создания данных. Тип `AppendBlob` не поддерживается.

Вы можете создать большой двоичный объект из локального файла, установить свойства приема в метаданные большого двоичного объекта и передать его следующим образом:

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Create the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob metadata and upload the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> Для использования пакета SDK для хранилища Azure Data Lake Gen2 требуется использовать `CreateFile` для отправки файлов и `Flush` в конце, если для параметра Close задано значение true.
> Подробный пример правильного использования пакета SDK для Data Lake Gen2 см. в разделе [Отправка файла с помощью пакета SDK для Azure Data Lake](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).

## <a name="delete-blobs-using-storage-lifecycle"></a>Удаление BLOB-объектов с помощью жизненного цикла хранилища

Обозреватель данных Azure не удаляет большие двоичные объекты после приема. Используйте [жизненный цикл хранилища BLOB-объектов Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением больших двоичных объектов. Рекомендуется, чтобы большие двоичные объекты сохранятся от трех до пяти дней.

## <a name="known-event-grid-issues"></a>Известные проблемы с сеткой событий

* При использовании обозреватель данных Azure для [экспорта](kusto/management/data-export/export-data-to-storage.md) файлов, используемых для приема в сетке событий, обратите внимание на следующее. 
    * Уведомления в службе "Сетка событий" не вызываются, если строка подключения, предоставленная для команды экспорта, или строка подключения, предоставленная [внешней таблице](kusto/management/data-export/export-data-to-an-external-table.md) , является связывающей строкой в [ADLS 2-го поколения формате](kusto/api/connection-strings/storage.md#azure-data-lake-store) (например, `abfss://filesystem@accountname.dfs.core.windows.net` ), но учетная запись хранения не включена для иерархического пространства имен. 
    * Если учетная запись не включена для иерархического пространства имен, в строке подключения должен использоваться формат [хранилища BLOB-объектов](kusto/api/connection-strings/storage.md#azure-storage-blob) (например, `https://accountname.blob.core.windows.net` ). Экспорт работает должным образом даже при использовании строки подключения ADLS 2-го поколения, но уведомления не будут активированы, и прием сетки событий не будет работать.

## <a name="next-steps"></a>Дальнейшие шаги

* [Прием больших двоичных объектов в службу Azure Data Explorer через подписку на уведомления службы "Сетка событий Azure"](ingest-data-event-grid.md)
* [Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью C #](data-connection-event-hub-csharp.md)
* [Создание подключения к данным в сетке событий для Azure обозреватель данных с помощью Python](data-connection-event-grid-python.md)
* [Создание подключения к данным в сетке событий для Azure обозреватель данных с помощью шаблона Azure Resource Manager](data-connection-event-grid-resource-manager.md)
