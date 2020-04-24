---
title: Добавление основок кластеров для Исследователя данных Azure с помощью Python
description: В этой статье вы узнаете, как добавить принципы кластеров для Azure Data Explorer с помощью Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 73956a73f5ae84529018abfce64c3a373b9a8ca1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496918"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-python"></a>Добавление основок кластеров для Исследователя данных Azure с помощью Python

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Шаблон Azure Resource Manager](cluster-principal-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы добавляете принципы кластеров для Azure Data Explorer с помощью Python.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Создание кластера](create-cluster-database-python.md).

## <a name="install-python-package"></a>Установка пакета Python

Чтобы установить пакет Python для Azure Data Explorer (Kusto), откройте окно командной строки с Python в пути. Выполните следующую команду:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Добавление основного кластера

Следующий пример показывает, как добавить основной кластер программно.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import ClusterPrincipalAssignment
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
#The cluster that is created as part of the Prerequisites
cluster_name = "mykustocluster"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.cluster_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, principal_assignment_name= principal_assignment_name, parameters=ClusterPrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenant_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscription_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор подписки, который используется для создания ресурсов.|
| client_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента приложения, который может получить доступ к ресурсам в вашем арендаторе.|
| client_secret | *xxxxxxxxxxxxxxxxxxxx* | Секрет клиента приложения, которое может получить доступ к ресурсам в вашем арендаторе. |
| resource_group_name | *testrg* | Название группы ресурсов, содержащей кластер.|
| cluster_name | *mykustocluster* | Название кластера.|
| principal_assignment_name | *кластерПринципназначения1* | Имя основного ресурса кластера.|
| principal_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Основным идентификатором, который может быть электронной почтой пользователя, идентификатором приложения или именем группы безопасности.|
| роль | *AllDatabasesAdmin* | Роль основного кластера, которая может быть 'AllDatabasesAdmin' или 'AllDatabasesViewer'.|
| tenant_id_for_principal | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор арендатора доверителя.|
| principal_type | *Приложение* | Тип принципала, который может быть 'Пользователь', 'App', или 'Группа'|

## <a name="next-steps"></a>Следующие шаги

* [Добавление основ базы данных](database-principal-python.md)
