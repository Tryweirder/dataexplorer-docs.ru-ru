---
title: Добавление основ базы данных для Исследователя данных Azure с помощью Python
description: В этой статье вы узнаете, как добавить принципы базы данных для Azure Data Explorer с помощью Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 3ddb34692158fdc7e6d4f7786f84b8c059bc0621
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498699"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Добавление основ базы данных для Исследователя данных Azure с помощью Python

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Шаблон Azure Resource Manager](database-principal-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы добавляете принципы базы данных для Azure Data Explorer с помощью Python.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Создание кластера и базы данных](create-cluster-database-python.md)

## <a name="install-python-package"></a>Установка пакета Python

Чтобы установить пакет Python для Azure Data Explorer (Kusto), откройте окно командной строки с Python в пути. Выполните следующую команду:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Добавление основной базы данных

Следующий пример показывает, как добавить основную базу данных программно.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
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
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenant_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscription_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор подписки, который используется для создания ресурсов.|
| client_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента приложения, который может получить доступ к ресурсам в вашем арендаторе.|
| client_secret | *xxxxxxxxxxxxxxxxxxxx* | Секрет клиента приложения, которое может получить доступ к ресурсам в вашем арендаторе. |
| resource_group_name | *testrg* | Название группы ресурсов, содержащей кластер.|
| cluster_name | *mykustocluster* | Название кластера.|
| database_name | *mykustodatabase* | Имя базы данных.|
| principal_assignment_name | *База данныхПринципиальноеназначение1* | Имя основного ресурса базы данных.|
| principal_id | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Основным идентификатором, который может быть электронной почтой пользователя, идентификатором приложения или именем группы безопасности.|
| роль | *Администрирование* | Роль основного специалиста базы данных, которая может быть 'Админ', 'Ingestor', 'Монитор', 'Пользователь', 'Неограниченные Зрители', 'Viewer'.|
| tenant_id_for_principal | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор арендатора доверителя.|
| principal_type | *Приложение* | Тип принципала, который может быть 'Пользователь', 'App', или 'Группа'|

## <a name="next-steps"></a>Следующие шаги

* [Проем данных с помощью библиотеки Azure Data Explorer Python](python-ingest-data.md)
