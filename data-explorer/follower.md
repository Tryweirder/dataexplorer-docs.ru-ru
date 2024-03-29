---
title: Использование функции базы данных следующих компонентов для присоединения баз данных в Azure обозреватель данных
description: Узнайте, как присоединить базы данных в Azure обозреватель данных с помощью функции базы данных ниже.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/06/2020
ms.openlocfilehash: 37e32e1fe84cd73f268de4400eec529815d3ce8f
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97514074"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Использование базы данных следующих служб для присоединения баз данных в Azure обозреватель данных

Функция **базы данных-след** позволяет присоединить базу данных, расположенную в другом кластере, к кластеру Azure обозреватель данных. **База данных-след** присоединяется в режиме *только для чтения* , что позволяет просматривать данные и выполнять запросы к данным, которые были переданы в **базу данных лидера**. База данных следов синхронизирует изменения в базах данных лидера. В связи с синхронизацией данные в доступности данных забывают в течение нескольких секунд. Продолжительность временной задержки зависит от общего размера метаданных базы данных лидера. Ведущие и последующие базы данных используют одну и ту же учетную запись хранения для выборки данных. Владельцем хранилища является лидер базы данных. База данных для получения результатов просматривает данные без необходимости их приема. Поскольку присоединенная база данных является базой данных только для чтения, данные, таблицы и политики в базе данных нельзя изменить, за исключением [политики кэширования](#configure-caching-policy), [участников](#manage-principals)и [разрешений](#manage-permissions). Не удается удалить подключенные базы данных. Они должны быть отсоединены от лидера или следующего, и только после этого их можно удалить. 

Присоединение базы данных к другому кластеру с помощью возможности последующей функции используется в качестве инфраструктуры для обмена данными между организациями и группами. Эта функция полезна для разделения ресурсов вычислений для защиты рабочей среды от нерабочих случаев использования. Кроме того, можно использовать для связи стоимости кластера Azure обозреватель данных с стороной, которая выполняет запросы к данным.

## <a name="which-databases-are-followed"></a>Какие базы данных будут выполнены?

* Кластер может соответствовать одной базе данных, нескольким базам данных или всем базам данных в кластере лидера. 
* Один кластер может следовать за базами данных из нескольких серверов-заполнителей. 
* Кластер может содержать как базы данных, так и лидерическую базу данных

## <a name="prerequisites"></a>Предварительные требования

1. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
1. [Создайте кластер и базу данных](create-cluster-database-portal.md) для лидера и последующих результатов.
1. Прием [данных](ingest-sample-data.md) в лидер базы данных с помощью одного из различных методов, обсуждаемых в [обзоре приема](./ingest-data-overview.md).

## <a name="attach-a-database"></a>Присоединение базы данных

Существует несколько методов, которые можно использовать для присоединения базы данных. В этой статье обсуждается подключение базы данных с помощью C#, Python, PowerShell или шаблона Azure Resource Manager. Чтобы присоединить базу данных, необходимо иметь учетную запись пользователя, группы, субъекта-службы или управляемую идентификацию по крайней мере с ролью участника в кластере лидерства и в кластере последующих действий. Назначения ролей можно добавлять и удалять с помощью [портала Azure](/azure/role-based-access-control/role-assignments-portal), [PowerShell](/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](/azure/role-based-access-control/role-assignments-cli) и [шаблона ARM](/azure/role-based-access-control/role-assignments-template). Вы можете узнать больше о [контроле доступа на основе ролей Azure (Azure RBAC)](/azure/role-based-access-control/overview) и [различных ролях](/azure/role-based-access-control/rbac-and-directory-admin-roles). 


# <a name="c"></a>[C#](#tab/csharp)

### <a name="attach-a-database-using-c"></a>Присоединение базы данных с помощью языка C #

#### <a name="needed-nugets"></a>Необходимые NuGet

* Установите пакет [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [Microsoft. RESTful. ClientRuntime. Azure. Authentication для проверки подлинности](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).

#### <a name="example"></a>Пример

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

# <a name="python"></a>[Python](#tab/python)

### <a name="attach-a-database-using-python"></a>Присоединение базы данных с помощью Python

#### <a name="needed-modules"></a>Необходимые модули

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="example"></a>Пример

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

# <a name="powershell"></a>[Оболочк](#tab/azure-powershell)

### <a name="attach-a-database-using-powershell"></a>Подключение базы данных с помощью PowerShell

#### <a name="needed-modules"></a>Необходимые модули

```
Install : Az.Kusto
```

#### <a name="example"></a>Пример

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "db"  ## Can be specific database name or * for all databases
$LeaderClustername = 'leader'
$LeaderClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$LeaderClusterResourceGroup = 'leaderResouceGroup'
$DefaultPrincipalsModificationKind = 'Union'
##Construct the LeaderClusterResourceId and Location
$getleadercluster = Get-AzKustoCluster -Name $LeaderClustername -ResourceGroupName $LeaderClusterResourceGroup -SubscriptionId $LeaderClusterSubscriptionID -ErrorAction Stop
$LeaderClusterResourceid = $getleadercluster.Id
$Location = $getleadercluster.Location
##Handle the config name if all databases needs to be followed
if($DatabaseName -eq '*')  {
        $configname = $FollowerClustername + 'config'
       } 
else {
        $configname = $DatabaseName   
     }
New-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername `
    -Name $configname `
    -ResourceGroupName $FollowerResourceGroupName `
    -SubscriptionId $FollowerClusterSubscriptionID `
    -DatabaseName $DatabaseName `
    -ClusterResourceId $LeaderClusterResourceid `
    -DefaultPrincipalsModificationKind $DefaultPrincipalsModificationKind `
    -Location $Location `
    -ErrorAction Stop 
```

# <a name="resource-manager-template"></a>[Шаблон Resource Manager](#tab/azure-resource-manager)

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Присоединение базы данных с помощью шаблона Azure Resource Manager

В этом разделе вы узнаете, как присоединить базу данных к существующей кластеру с помощью [шаблона Azure Resource Manager](/azure/azure-resource-manager/management/overview). 

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
            "apiVersion": "2020-02-15",
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

Шаблон Azure Resource Manager можно развернуть с [помощью портал Azure](https://portal.azure.com) или с помощью PowerShell.

   ![Развертывание шаблона](media/follower/template-deployment.png)

|**Параметр**  |**Описание**  |
|---------|---------|
|Имя кластера следов     |  Имя кластера последов; где будет развернут шаблон.  |
|Имя конфигурации присоединенной базы данных    |    Имя объекта конфигурации присоединенной базы данных. Имя может быть любой строкой, уникальной на уровне кластера.     |
|Имя базы данных     |      Имя базы данных, которая будет следовать. Если вы хотите следовать всем базам данных лидера, используйте "*".   |
|Идентификатор ресурса кластера лидера    |   Идентификатор ресурса для кластера лидера.      |
|Тип изменения участников по умолчанию    |   Тип изменения субъекта по умолчанию. Может иметь `Union` `Replace` или `None` . Дополнительные сведения об изменении типа субъекта по умолчанию см. в разделе [команда управления типа изменения основного сервера](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind).      |
|Расположение   |   Расположение всех ресурсов. Лидер и след должны находиться в одном расположении.       |

---

## <a name="verify-that-the-database-was-successfully-attached"></a>Проверка успешного присоединения базы данных

Чтобы убедиться, что база данных была успешно присоединена, найдите подключенные базы данных в [портал Azure](https://portal.azure.com). Вы можете убедиться, что базы данных были успешно присоединены в кластерах " [след](#check-your-follower-cluster) " или " [лидер](#check-your-leader-cluster) ".

### <a name="check-your-follower-cluster"></a>Проверка кластера последующих действий  

1. Перейдите к кластеру последующих узлов и выберите **базы данных** .
1. Поиск новых баз данных, которые доступны только для чтения в списке

    ![База данных, доступная только для чтения](media/follower/read-only-follower-database.png)

### <a name="check-your-leader-cluster"></a>Проверка кластера лидерства

1. Перейдите к кластеру лидерства и выберите **базы данных** .
2. Убедитесь, что соответствующие базы данных помечены как **Общие с другими**  >  **Да**

    ![Чтение и запись подключенных баз данных](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database"></a>Отсоединение базы данных следующих следов  

> [!NOTE]
> Чтобы отсоединить базу данных от службы "следующий" или "лидер", у вас должен быть пользователь, группа, субъект-служба или управляемое удостоверение с ролью участника в кластере, из которого отсоединяется база данных. В приведенном ниже примере используется субъект-служба.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-c"></a>Отсоединение присоединенной базы данных последующих действий от кластера последующих действий с помощью C #


Кластер последов может отключить все подключенные базы данных следующего, как показано ниже.

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

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-c"></a>Отсоединение присоединенной базы данных следующих серверов из кластера лидера с помощью C #

Кластер лидера может отключить любую присоединенную базу данных следующим образом:

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

# <a name="python"></a>[Python](#tab/python)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-python"></a>Отсоединение присоединенной базы данных следующих серверов из кластера последующих узлов с помощью Python

Кластер последующих действий может отключить любую присоединенную базу данных следующим образом:

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

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-python"></a>Отсоединение присоединенной базы данных следующих серверов из кластера лидера с помощью Python

Кластер лидера может отключить любую присоединенную базу данных следующим образом:

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

# <a name="powershell"></a>[Оболочк](#tab/azure-powershell)

### <a name="detach-a-database-using-powershell"></a>Отсоединение базы данных с помощью PowerShell

#### <a name="needed-modules"></a>Необходимые модули

```
Install : Az.Kusto
```

#### <a name="example"></a>Пример

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "sanjn"  ## Can be specific database name or * for all databases

##Construct the Configuration name 
$confignameraw = (Get-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -ResourceGroupName $FollowerResourceGroupName -SubscriptionId $FollowerClusterSubscriptionID) | Where-Object {$_.DatabaseName -eq $DatabaseName }
$configname =$confignameraw.Name.Split("/")[1]

Remove-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -Name $configname -ResourceGroupName $FollowerResourceGroupName
```

# <a name="resource-manager-template"></a>[Шаблон Resource Manager](#tab/azure-resource-manager)

[Отсоедините базу данных следующих](#detach-the-follower-database) типов с помощью C#, Python или PowerShell.

---

## <a name="manage-principals-permissions-and-caching-policy"></a>Управление участниками, разрешениями и политикой кэширования

### <a name="manage-principals"></a>Управление участниками

При присоединении базы данных укажите параметр **"тип изменения участников по умолчанию"**. По умолчанию сбор данных о лидере [полномочных участников](kusto/management/access-control/index.md#authorization) сохраняется.

|**Вид** |**Описание**  |
|---------|---------|
|**Union**     |   Присоединенные участники базы данных всегда включают в себя исходные субъекты базы данных, а также дополнительные новые участники, добавленные в базу данных следующих.      |
|**Заменить**   |    Нет наследования субъектов от исходной базы данных. Для присоединенной базы данных необходимо создать новые субъекты.     |
|**Нет**   |   Присоединенные участники базы данных включают только субъекты исходной базы данных без дополнительных субъектов.      |

Дополнительные сведения об использовании команд управления для настройки полномочных участников см. в разделе [Управление командами для управления кластером последующих действий](kusto/management/cluster-follower.md).

### <a name="manage-permissions"></a>Управление разрешениями

Управление разрешением на доступ к базе данных только для чтения осуществляется так же, как и для всех типов баз данных. См. раздел [Управление разрешениями в портал Azure](manage-database-permissions.md#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Настройка политики кэширования

Администратор базы данных может изменить [политику кэширования](kusto/management/cache-policy.md) присоединенной базы данных или любой из ее таблиц в кластере размещения. По умолчанию заполнение коллекции баз данных и политик кэширования на уровне таблицы. Например, можно использовать политику кэширования в 30 дней в базе данных лидера для выполнения ежемесячного создания отчетов и политику кэширования в течение трех дней в базе данных последующих версий, чтобы запросить только последние данные для устранения неполадок. Дополнительные сведения об использовании команд управления для настройки политики кэширования для базы данных или следующей таблицы см. в разделе [Управление командами для управления кластером последующих действий](kusto/management/cluster-follower.md).

## <a name="notes"></a>Примечания

* При возникновении конфликтов между базами данных в кластерах «руководитель» и «следующий», если за всеми базами данных последовал кластер-след, они разрешаются следующим образом:
  * База данных с именем *DB* , созданная в кластере последующих действий, имеет приоритет над базой данных с тем же именем, созданным в кластере лидера. Поэтому необходимо удалить *базу данных в* кластере для последующих узлов (или переименовать ее), чтобы *получить базу данных лидера.*
  * База данных с именем *DB* , за которой следуют два или больше кластеров-заполнителей, будет произвольно выбрана из *одного* из кластеров-заполнителей и не будет следовать более одного раза.
* Команды для отображения [журнала действий кластера и](kusto/management/systeminfo.md) журнала, выполняемого в последующих кластерах, будут показывать действия и историю в кластере-следе, и их результирующие наборы не будут включать в себя кластеры лидера.
  * Например `.show queries` , команда, выполняемая в кластере последующих действий, будет показывать только запросы, запущенные в базах данных, за которыми следует кластер-след, а не запросы, выполняемые к одной и той же базе данных в кластере лидерства.
  
## <a name="limitations"></a>Ограничения

* Следующие и ведущие кластеры должны находиться в одном регионе.
* Прием [потоковой передачи](ingest-data-streaming.md) нельзя использовать для базы данных, которая находится в процессе.
* Шифрование данных с помощью [управляемых пользователем ключей](security.md#customer-managed-keys-with-azure-key-vault) не поддерживается как в лидерах, так и в кластерах-следах. 
* Невозможно удалить базу данных, присоединенную к другому кластеру, прежде чем отсоединить ее.
* Невозможно удалить кластер с базой данных, присоединенной к другому кластеру, прежде чем отсоединить его.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о конфигурации кластера ниже см. в разделе [управляющие команды для управления кластером последующих действий](kusto/management/cluster-follower.md).
