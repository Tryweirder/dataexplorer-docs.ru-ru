---
title: 'Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью C #'
description: Из этой статьи вы узнаете, как создать подключение к данным концентратора событий для Azure обозреватель данных с помощью C#.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: d71dabaa3ed597b641d0e16c2ff152779fda2d53
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201381"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью C #

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
В этой статье вы создадите подключение к данным концентратора событий для Azure обозреватель данных с помощью C#.

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера и базы данных](create-cluster-database-csharp.md)
* Создание [сопоставления таблиц и столбцов](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Задание [политик базы данных и таблиц](database-table-policies-csharp.md) (необязательно)
* Создание [концентратора событий с данными для приема](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Добавление подключения к данным концентратора событий

В следующем примере показано, как добавить подключение к данным концентратора событий программным способом. Дополнительные сведения о добавлении подключения к данным концентратора событий с помощью портал Azure см. в разделе [Подключение к концентратору событий](ingest-data-event-hub.md#connect-to-the-event-hub) .

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenantId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор подписки, используемый для создания ресурсов.|
| clientid | *xxxxxxxx-XXXXX-XXXX-XXXX-XXXXXXXXX* | Идентификатор клиента приложения, которое может получать доступ к ресурсам в клиенте.|
| clientSecret | *кскскскскскскскскскскскскскс* | Секрет клиента приложения, которое может получить доступ к ресурсам в клиенте.|
| имя_группы_ресурсов | *testrg* | Имя группы ресурсов, содержащей кластер.|
| clusterName | *mykustocluster* | Имя кластера.|
| databaseName | *mykustodatabase* | Имя целевой базы данных в кластере.|
| датаконнектионнаме | *мевенсубконнект* | Требуемое имя подключения к данным.|
| tableName | *стормевентс* | Имя целевой таблицы в целевой базе данных.|
| маппингруленаме | *StormEvents_CSV_Mapping* | Имя сопоставления столбцов, связанного с целевой таблицей.|
| Формат. | *-* | Формат данных сообщения.|
| евенсубресаурцеид | *Идентификатор ресурса* | Идентификатор ресурса концентратора событий, который содержит данные для приема. |
| consumerGroup | *$Default* | Группа потребителей концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения к данным.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
