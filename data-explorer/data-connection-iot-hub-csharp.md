---
title: 'Создание соединения концентратора IoT для исследователя данных Azure с помощью C #'
description: В этой статье вы узнаете, как создать соединение концентратора IoT для Исследователя данных Azure, используя C-.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1b2c74155ba1cb26cb6dd9ca40f9f8c0deda2646
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497724"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Создание соединения концентратора IoT для исследователя данных Azure с помощью C -S (Preview)

> [!div class="op_single_selector"]
> * [Портал](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Шаблон Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure Data Explorer предлагает проглатывание (загрузку данных) из концентраторов событий, концентраторов IoT и капли, написанные в контейнеры с каплями. В этой статье вы создаете соединение данных Концентратора IoT для Исследователя данных Azure с помощью C..

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что вы **включите разработку Azure** во время установки Visual Studio.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера и базы данных](create-cluster-database-csharp.md)
* Создание [отображения таблицы и столбца](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Установка [политик базы данных и таблиц](database-table-policies-csharp.md) (необязательно)
* Создание [концентратора IoT с настроенной общей политикой доступа.](ingest-data-iot-hub.md#create-an-iot-hub)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Добавление соединения концентратора IoT 

Ниже приводится следующий пример, как программно добавить соединение данных Концентратора IoT. [Спомощью таблицы Azure Data Explorer с IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) можно добавить подключение к концентратору Iot с помощью портала Azure.

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
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
| iotHubResourceId | *Идентификатор ресурса* | Идентификатор ресурса вашего концентратора IoT, в мещавх данных для приема. |
| sharedAccessPolicyName | *iothubforread* | Название политики общего доступа, определяющей разрешения на подключение устройств и служб к Концентратору IoT. |
| consumerGroup | *$Default* | Группа потребителей вашего концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения данных.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
