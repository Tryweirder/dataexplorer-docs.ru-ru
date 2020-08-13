---
title: 'Создание подключения к данным в сетке событий для обозреватель данных Azure с помощью C #'
description: Из этой статьи вы узнаете, как создать подключение к данным в сетке событий для Azure обозреватель данных с помощью C#.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 597eb522da9e3cb383cb3c0aad5ccc7c78de6ce3
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201388"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Создание подключения к данным в сетке событий для обозреватель данных Azure с помощью C #

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-grid-resource-manager.md)


[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
 В этой статье вы создадите подключение к данным в сетке событий для Azure обозреватель данных с помощью C#.

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера и базы данных](create-cluster-database-csharp.md)
* Создание [сопоставления таблиц и столбцов](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Задание [политик базы данных и таблиц](database-table-policies-csharp.md) (необязательно)
* Создайте [учетную запись хранения с подпиской службы "Сетка событий](ingest-data-event-grid.md)".

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Добавление подключения к данным в сетке событий

В следующем примере показано, как добавить подключение к данным в сетке событий программным способом. См. раздел [Создание подключения к данным в сетке событий в Azure обозреватель данных](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) для добавления подключения к данным сетки событий с помощью портал Azure.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenantId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор подписки, используемый для создания ресурсов.|
| clientid | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента приложения, которое может получать доступ к ресурсам в клиенте.|
| clientSecret | *кскскскскскскскскскскскскскс* | Секрет клиента приложения, которое может получить доступ к ресурсам в клиенте. |
| имя_группы_ресурсов | *testrg* | Имя группы ресурсов, содержащей кластер.|
| clusterName | *mykustocluster* | Имя кластера.|
| databaseName | *mykustodatabase* | Имя целевой базы данных в кластере.|
| датаконнектионнаме | *мевенсубконнект* | Требуемое имя подключения к данным.|
| tableName | *стормевентс* | Имя целевой таблицы в целевой базе данных.|
| маппингруленаме | *StormEvents_CSV_Mapping* | Имя сопоставления столбцов, связанного с целевой таблицей.|
| Формат. | *-* | Формат данных сообщения.|
| евенсубресаурцеид | *Идентификатор ресурса* | Идентификатор ресурса концентратора событий, в котором сетка событий настроена для отправки событий. |
| сторажеаккаунтресаурцеид | *Идентификатор ресурса* | Идентификатор ресурса учетной записи хранения, в которой хранятся данные для приема. |
| consumerGroup | *$Default* | Группа потребителей концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения к данным.|

## <a name="generate-sample-data"></a>Создание примера данных

Теперь, когда Azure обозреватель данных и учетная запись хранения подключены, можно создать пример данных и передать их в хранилище.

> [!NOTE]
> Azure обозреватель данных не удалит прием BLOB-объектов после приема. Храните большие двоичные объекты в течение трех – пять дней, используя [жизненный цикл хранилища BLOB-объектов Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением больших двоичных объектов.

### <a name="upload-file-using-azure-blob-storage-sdk"></a>Отправка файла с помощью пакета SDK для хранилища BLOB-объектов Azure

Следующий фрагмент кода создает новый контейнер в учетной записи хранения, отправляет существующий файл (в виде большого двоичного объекта) в этот контейнер, а затем перечисляет большие двоичные объекты в контейнере.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName = <container_name>;
var blobName = <blob_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mappingReference>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
blob.Metadata.Add("kustoIngestionMappingReference", mapping);
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

### <a name="upload-file-using-azure-data-lake-sdk"></a>Отправка файла с помощью пакета SDK для Azure Data Lake

При работе с Data Lake Storage 2-го поколения для отправки файлов в хранилище можно использовать [пакет SDK для Azure Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) . Следующий фрагмент кода создает новую файловую систему в хранилище Azure Data Lake и передает в нее локальный файл с метаданными.

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var fileName = <file_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mapping_reference>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Create the filesystem and an empty file
var dataLakeFileSystemClient = dataLakeServiceClient.CreateFileSystem(fileSystemName).Value;
var dataLakeFileClient = dataLakeFileSystemClient.CreateFile(fileName).Value;

// Set metadata
IDictionary<String, String> metadata = new Dictionary<string, string>();
metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
metadata.Add("kustoIngestionMappingReference", mapping);
dataLakeFileClient.SetMetadata(metadata);

// Write to the file and close it
var fileStream = File.OpenRead(localFileName);
var fileSize = fileStream.Length;
dataLakeFileClient.Append(fileStream, offset: 0);
dataLakeFileClient.Flush(position: fileSize, close: true); // Note: This line triggers the event being processed by the data connection
```

> [!NOTE]
> При использовании [пакета SDK Azure Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) для отправки файла первый вызов функции [CreateFile](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createfile?view=azure-dotnet) активирует событие сетки событий с размером 0, и это событие игнорируется службой Azure обозреватель данных. При вызове функции flush с параметром "Close", для которого задано значение "true", запускается другое событие. Это событие указывает на то, что это Последнее обновление, и файловый поток был закрыт. Это событие обрабатывается подключением к данным в сетке событий. Дополнительные сведения о сбросе см. в разделе [метод Azure Data Lake Flush](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flush?view=azure-dotnet).

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
