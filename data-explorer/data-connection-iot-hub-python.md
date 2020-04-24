---
title: Создание соединения концентратора IoT для исследователя данных Azure с помощью Python
description: В этой статье вы узнаете, как создать соединение концентратора IoT для Azure Data Explorer с помощью Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4357e53a36274aaefdfc379c73a0f2dc1e0e2bbc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497685"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Создание соединения концентратора IoT для исследователя данных Azure с помощью Python (Preview)

> [!div class="op_single_selector"]
> * [Портал](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Шаблон Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

В этой статье вы создаете соединение данных Концентратора IoT для Azure Data Explorer с помощью Python. Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure Data Explorer предлагает проглатывание или загрузку данных из концентраторов событий, Концентраторов IoT и капли, написанные в контейнеры с каплями.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.

* [Python версии 3.4+](https://www.python.org/downloads/).

* [Кластер и база данных](create-cluster-database-python.md).

* [Таблица и столбец отображение](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Политики баз данных и таблиц](database-table-policies-python.md) (необязательно).

* [Концентратор IoT с настроенной общей политикой доступа.](ingest-data-iot-hub.md#create-an-iot-hub)

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Добавление соединения концентратора IoT 

Ниже приводится следующий пример, как программно добавить соединение данных Концентратора IoT. [Спомощью таблицы Azure Data Explorer с IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) можно добавить подключение к концентратору Iot с помощью портала Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenant_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор подписки, который используется для создания ресурсов.|
| client_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента приложения, который может получить доступ к ресурсам в вашем арендаторе.|
| client_secret | *xxxxxxxxxxxxxxxxxxxx* | Секрет клиента приложения, которое может получить доступ к ресурсам в вашем арендаторе. |
| resource_group_name | *testrg* | Название группы ресурсов, содержащей кластер.|
| cluster_name | *mykustocluster* | Название кластера.|
| database_name | *mykustodatabase* | Имя целевой базы данных в кластере.|
| data_connection_name | *myeventhubconnect* | Нужное название подключения к данным.|
| имя_таблицы | *StormEvents* | Имя целевой таблицы в целевой базе данных.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Имя отображения столбца, отображение которого относится к целевой таблице.|
| data_format | *Csv* | Формат данных сообщения.|
| iot_hub_resource_id | *Идентификатор ресурса* | Идентификатор ресурса вашего концентратора IoT, в мещавх данных для приема.|
| shared_access_policy_name | *iothubforread* | Название политики общего доступа, определяющей разрешения на подключение устройств и служб к Концентратору IoT. |
| consumer_group | *$Default* | Группа потребителей вашего концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения данных.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
