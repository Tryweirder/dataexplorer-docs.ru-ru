---
title: Создание соединения концентратора событий для исследователя данных Azure с помощью Python
description: В этой статье вы узнаете, как создать соединение данных концентратора событий для Azure Data Explorer с помощью Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 36696a6fcdd74dc86705859ba62b608e9aa2ccf9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495332"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Создание соединения концентратора событий для исследователя данных Azure с помощью Python

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-hub-resource-manager.md)

В этой статье вы создаете подключение к данным Концентратора событий для Azure Data Explorer с помощью Python. Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure Data Explorer предлагает проглатывание или загрузку данных из концентраторов событий, Концентраторов IoT и капли, написанные в контейнеры с каплями.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) бесплатно.

* [Python версии 3.4+](https://www.python.org/downloads/).

* [Кластер и база данных](create-cluster-database-python.md).

* [Таблица и столбец отображение](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Политики баз данных и таблиц](database-table-policies-python.md) (необязательно).

* [Концентратор событий с данными для приема.](ingest-data-event-hub.md#create-an-event-hub)

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Добавление подключения концентратора событий

В следующем примере показано, как программно добавлять подключение к концентратору событий. [Свяжетесь с концентратором событий](ingest-data-event-hub.md#connect-to-the-event-hub) для добавления подключения к вцентром событий с помощью портала Azure.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
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
| event_hub_resource_id | *Идентификатор ресурса* | Идентификатор ресурса вашего концентратора событий, в мещающий данные для приема. |
| consumer_group | *$Default* | Группа потребителей вашего концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения данных.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
