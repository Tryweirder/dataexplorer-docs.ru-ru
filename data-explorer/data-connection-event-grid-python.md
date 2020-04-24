---
title: Создание подключения данных сетки событий для исследователя данных Azure с помощью Python
description: В этой статье вы узнаете, как создать подключение к данным Event Grid для Azure Data Explorer с помощью Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 3d0634819eda78b9cf5fa1d135c82b906363ef90
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497035"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Создание подключения данных сетки событий для исследователя данных Azure с помощью Python

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-grid-resource-manager.md)

В этой статье вы создаете подключение данных Event Grid для Azure Data Explorer с помощью Python. Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Azure Data Explorer предлагает проглатывание или загрузку данных из концентраторов событий, Концентраторов IoT и капли, написанные в контейнеры с каплями.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python версии 3.4+](https://www.python.org/downloads/).

* [Кластер и база данных](create-cluster-database-python.md).

* [Таблица и столбец отображение](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Политики баз данных и таблиц](database-table-policies-csharp.md) (необязательно).

* [Учетная запись хранения с подпиской Event Grid.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Добавление подключения к данным Event Grid

Следующий пример показывает, как можно программно добавить подключение к данным Event Grid. [Прослежвините подключение данных Event Grid в Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) для добавления подключения данных Event Grid с помощью портала Azure.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenant_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscription_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор подписки, который используется для создания ресурсов.|
| client_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента приложения, который может получить доступ к ресурсам в вашем арендаторе.|
| client_secret | *xxxxxxxxxxxxxxxxxxxx* | Секрет клиента приложения, которое может получить доступ к ресурсам в вашем арендаторе. |
| resource_group_name | *testrg* | Название группы ресурсов, содержащей кластер.|
| cluster_name | *mykustocluster* | Название кластера.|
| database_name | *mykustodatabase* | Имя целевой базы данных в кластере.|
| data_connection_name | *myeventhubconnect* | Нужное название подключения к данным.|
| имя_таблицы | *StormEvents* | Имя целевой таблицы в целевой базе данных.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Имя отображения столбца, отображение которого относится к целевой таблице.|
| data_format | *Csv* | Формат данных сообщения.|
| event_hub_resource_id | *Идентификатор ресурса* | Идентификатор ресурса вашего концентратора событий, где настроена сетка событий для отправки событий. |
| storage_account_resource_id | *Идентификатор ресурса* | Идентификатор ресурса учетной записи хранилища, в мещах данных для приема. |
| consumer_group | *$Default* | Группа потребителей вашего концентратора событий.|
| location | *Центральная часть США* | Расположение ресурса подключения данных.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
