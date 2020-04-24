---
title: Используйте функцию базы данных для присоединения баз данных в Azure Data Explorer
description: Узнайте о том, как прикрепить базы данных в Azure Data Explorer с помощью функции базы данных последователя.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4c385551f8983219dddf840f7a2d590809b030a3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496723"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Используйте базу данных последователя для присоединения баз данных в Azure Data Explorer

Функция **базы данных последователя** позволяет прикрепить базу данных, расположенную в другом кластере, к кластеру Azure Data Explorer. **База данных последователя** прикрепляется в режиме только для *чтения,* что позволяет просматривать данные и запускать запросы на данных, которые были попадены в **базу данных лидера.** База данных последователя синхронизирует изменения в базах данных лидеров. Из-за синхронизации, есть отставание данных от нескольких секунд до нескольких минут в доступности данных. Продолжительность временного лага зависит от общего размера метаданных базы данных лидеров. Базы данных лидера и последователя используют одну и ту же учетную запись для получения данных. Хранилище принадлежит базе данных лидеров. База данных последователя просматривает данные без необходимости их глотать. Поскольку прилагаемые базы данных являются базой данных только для чтения, данные, таблицы и политики в базе данных не могут быть изменены, за исключением [политики кэширования,](#configure-caching-policy) [принципов](#manage-principals)и [разрешений.](#manage-permissions) Прилагаемые базы данных не могут быть удалены. Они должны быть отделены лидером или последователем, и только тогда они могут быть удалены. 

Присоединение базы данных к другому кластеру с использованием возможностей последователя используется в качестве инфраструктуры для обмена данными между организациями и группами. Функция полезна для сегрегировать вычислительные ресурсы для защиты производственной среды от случаев непроизводственного использования. Последователь также может быть использован для привязки стоимости кластера Azure Data Explorer с группой, которая выполняет запросы на данных.

## <a name="which-databases-are-followed"></a>Какие базы данных следуют?

* Кластер может следовать одной базе данных, нескольким базам данных или всем базам данных кластера лидеров. 
* Один кластер может следовать базам данных из нескольких кластеров лидеров. 
* Кластер может содержать как базы данных последователя, так и базы данных лидеров

## <a name="prerequisites"></a>Предварительные требования

1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
1. [Создайте кластер и DB](/azure/data-explorer/create-cluster-database-portal) для лидера и последователя.
1. [Прием данных](/azure/data-explorer/ingest-sample-data) в базу данных лидеров с использованием одного из различных методов, обсуждаемых в [обзоре приема.](/azure/data-explorer/ingest-data-overview)

## <a name="attach-a-database"></a>Присоединение базы данных

Существуют различные методы, которые можно использовать для присоединения базы данных. В этой статье мы обсуждаем присоединение базы данных с использованием шаблона СИ или менеджера ресурсов Azure. Чтобы прикрепить базу данных, необходимо иметь разрешения на кластер лидеров и кластер последователя. Для получения дополнительной информации [manage permissions](#manage-permissions)о разрешениях см.

### <a name="attach-a-database-using-c"></a>Прикрепите базу данных с помощью C #

#### <a name="needed-nugets"></a>Нужны NuGets

* Установка [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [Microsoft.Rest.ClientRuntime.Azure.Authentication для проверки подлинности.](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)

#### <a name="code-example"></a>Пример кода

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Прикрепите базу данных с помощью Python

#### <a name="needed-modules"></a>Необходимые модули

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Пример кода

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Прикрепите базу данных с помощью шаблона управления ресурсами Azure

В этом разделе вы научитесь прикреплять базу данных к существующему cluser с помощью [шаблона Azure Resource Manager.](/azure/azure-resource-manager/management/overview) 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>Развертывание шаблона 

Шаблон управления ресурсами Azure можно развернуть [с помощью портала Azure](https://portal.azure.com) или с помощью powershell.

   ![развертывание шаблонов](media/follower/template-deployment.png)


|**Параметр**  |**Описание**  |
|---------|---------|
|Имя последователя кластера     |  Название кластера последователя; где будет развернут шаблон.  |
|Прилагаемое название конфигураций баз данных    |    Имя объекта конфигураций прилагаемых баз данных. Имя может быть любой строкой, которая уникальна на уровне кластера.     |
|Имя базы данных     |      Название базы данных, которой следует следовать. Если вы хотите следить за всеми базами данных лидера, используйте «К».   |
|Идентификатор кластерных ресурсов лидера    |   Идентификатор ресурса кластера лидеров.      |
|По умолчанию Принципы Модификация Вид    |   Основной вид модификации по умолчанию. Может `Union` `Replace` быть, `None`или . Для получения дополнительной информации о принципиальной модификации по умолчанию [см.](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind)      |
|Расположение   |   Расположение всех ресурсов. Лидер и последователь должны находиться в одном месте.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Убедитесь, что база данных была успешно прикреплена

Чтобы убедиться, что база данных была успешно прикреплена, найдите прилагаемые базы данных на [портале Azure.](https://portal.azure.com) 

1. Перейдите к кластеру последователя и выберите **базы данных**
1. Поиск новых баз данных только для чтения в списке баз данных.

    ![База данных только для чтения](media/follower/read-only-follower-database.png)

Еще один вариант:

1. Перейдите к кластеру лидеров и выберите **базы данных**
2. Убедитесь, что соответствующие базы данных помечены как **ОБЩИЕ С ДРУГИЕ** > **ДА**

    ![Читать и писать прилагаемые базы данных](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>Отсоедините базу данных последователя с помощью C # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Отсоедините прилагаемую базу данных последователя из кластера последователя

Кластер последователя может отсоединить любую прилагаемую базу данных следующим образом:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Отсоедините прилагаемую базу данных последователя из кластера лидеров

Кластер лидеров может отсоединить любую прилагаемую базу данных следующим образом:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>Отсоедините базу данных последователя с помощью Python

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>Отсоедините прилагаемую базу данных последователя из кластера последователя

Кластер последователя может отсоединить любую прилагаемую базу данных следующим образом:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>Отсоедините прилагаемую базу данных последователя из кластера лидеров

Кластер лидеров может отсоединить любую прилагаемую базу данных следующим образом:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Управление принципами, разрешениями и политикой кэширования

### <a name="manage-principals"></a>Управление принципами

При подключении базы данных укажите **"вид изменения принципов по умолчанию".** По умолчанию ведется сбор данных о базах данных лидеров [авторизованных принципов](kusto/management/access-control/index.md#authorization)

|**Вид** |**Описание**  |
|---------|---------|
|**Union**     |   Прилагаемые принципы базы данных всегда будут включать в себя исходные принципы базы данных плюс дополнительные новые принципы, добавленные в базу данных последователя.      |
|**Заменить**   |    Нет наследования директоров из исходной базы данных. Для прилагаемых баз данных должны быть созданы новые принципы.     |
|**Нет**   |   Прилагаемые принципы базы данных включают в себя только принципы исходной базы данных без каких-либо дополнительных принципов.      |

Для получения дополнительной информации об использовании команд управления [Control commands for managing a follower cluster](kusto/management/cluster-follower.md)для настройки авторизованных принципов см.

### <a name="manage-permissions"></a>Управление разрешениями

Управление разрешением базы данных только для чтения является таким же, как и для всех типов баз данных. Смотрите [разрешения управления на портале Azure](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Настройка политики кэширования

Администратор базы данных последователя может изменить [политику кэширования](kusto/management/cache-policy.md) прилагаемых баз данных или любой из ее таблиц в кластере хостинга. По умолчанию ведется сбор данных данных лидеров политик кэширования баз данных и таблицы. Например, в базе данных лидеров может быть 30-дневная политика кэширования для запуска ежемесячной отчетности и трехдневная политика кэширования в базе данных последователя для запроса только последних данных для устранения неполадок. Для получения дополнительной информации об использовании команд управления для настройки политики кэширования в базе данных или таблице последователя [см.](kusto/management/cluster-follower.md)

## <a name="limitations"></a>Ограничения

* Последователь и группы лидеров должны находиться в одном регионе.
* [Потоковая передача](/azure/data-explorer/ingest-data-streaming) не может быть использована в заготовительной базе данных.
* Шифрование данных с помощью [управляемых клиентами ключей](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) не поддерживается как в кластерах лидеров, так и в кластерах последователей. 
* Вы не можете удалить базу данных, которая присоединена к другому кластеру, прежде чем отсоединить ее.
* Нельзя удалить кластер с базой данных, прикрепленной к другому кластеру, прежде чем отсоединить его.
* Нельзя остановить кластер, который прикрепил базу данных последователя или лидера (ы). 

## <a name="next-steps"></a>Следующие шаги

* Для получения информации о конфигурации кластера последователь, [см. Управление командами для управления кластером последователя.](kusto/management/cluster-follower.md)
