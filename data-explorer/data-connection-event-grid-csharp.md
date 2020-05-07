---
title: 'Создание подключения данных event Grid для Azure Data Explorer с помощью C #'
description: В этой статье вы узнаете, как создать подключение к данным Event Grid для Azure Data Explorer с помощью C..
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: d2432d302640d20bb199972bc686dadab41278f1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497815"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Создание подключения данных event Grid для Azure Data Explorer с помощью C #

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-grid-resource-manager.md)


Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure Data Explorer предлагает проглатывание (загрузку данных) из концентраторов событий, концентраторов IoT и капли, написанные в контейнеры с каплями. В этой статье вы создаете подключение данных Event Grid для Azure Data Explorer с помощью C..

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что вы **включите разработку Azure** во время установки Visual Studio.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера и базы данных](create-cluster-database-csharp.md)
* Создание [отображения таблицы и столбца](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Установка [политик базы данных и таблиц](database-table-policies-csharp.md) (необязательно)
* Создайте [учетную запись хранения с подпиской Event Grid.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Добавление подключения к данным Event Grid

Следующий пример показывает, как можно программно добавить подключение к данным Event Grid. [Прослежвините подключение данных Event Grid в Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) для добавления подключения данных Event Grid с помощью портала Azure.

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
| tenantId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор подписки, который используется для создания ресурсов.|
| clientid | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента приложения, который может получить доступ к ресурсам в вашем арендаторе.|
| clientSecret | *xxxxxxxxxxxxxxxxxxxx* | Секрет клиента приложения, которое может получить доступ к ресурсам в вашем арендаторе. |
| имя_группы_ресурсов | *testrg* | Название группы ресурсов, содержащей кластер.|
| clusterName | *mykustocluster* | Название кластера.|
| databaseName | *mykustodatabase* | Имя целевой базы данных в кластере.|
| dataConnectionName | *myeventhubconnect* | Нужное название подключения к данным.|
| tableName | *StormEvents* | Имя целевой таблицы в целевой базе данных.|
| отображениеRuleName | *StormEvents_CSV_Mapping* | Имя отображения столбца, отображение которого относится к целевой таблице.|
| данныеФормат | *Csv* | Формат данных сообщения.|
| eventHubResourceId | *Идентификатор ресурса* | Идентификатор ресурса вашего концентратора событий, где настроена сетка событий для отправки событий. |
| хранениеAccountResourceId | *Идентификатор ресурса* | Идентификатор ресурса учетной записи хранилища, в мещах данных для приема. |
| consumerGroup | *$Default* | Группа потребителей вашего концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения данных.|

## <a name="generate-sample-data"></a>Создание примера данных

Теперь, когда Azure Data Explorer и учетная запись хранения подключены, вы можете создать примеры данных и отправить их в хранилище BLOB-объектов.

Этот скрипт создает новый контейнер в вашей учетной записи хранения, передает существующий файл (как большой двоичный объект) в этот контейнер и выводит список больших двоичных объектов в контейнере.

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

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure Data Explorer не будет удалять проглатку капли. Сохраняйте капли в течение трех-пяти дней, используя [жизненный цикл хранения Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) для управления удалением капли.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]