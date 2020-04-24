---
title: 'Добавление основок кластеров для Azure Data Explorer с помощью C #'
description: В этой статье вы узнаете, как добавить принципы кластеров для Azure Data Explorer с помощью C..
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: b677768b87419833968de48eca2dd32c4fdb17cb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496840"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Добавление основок кластеров для Azure Data Explorer с помощью C #

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Шаблон Azure Resource Manager](cluster-principal-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы добавляете принципы кластеров для Azure Data Explorer с помощью C'.

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что вы **включите разработку Azure** во время установки Visual Studio.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Создание кластера](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Установка СЗ NuGet

* Установка [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) для проверки подлинности.

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Добавление основного кластера

Следующий пример показывает, как добавить основной кластер программно.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenantId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор подписки, который используется для создания ресурсов.|
| clientid | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента приложения, который может получить доступ к ресурсам в вашем арендаторе.|
| clientSecret | *xxxxxxxxxxxxxxxxxxxx* | Секрет клиента приложения, которое может получить доступ к ресурсам в вашем арендаторе. |
| имя_группы_ресурсов | *testrg* | Название группы ресурсов, содержащей кластер.|
| clusterName | *mykustocluster* | Название кластера.|
| mainAssignmentName | *кластерПринципназначения1* | Имя основного ресурса кластера.|
| principalId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Основным идентификатором, который может быть электронной почтой пользователя, идентификатором приложения или именем группы безопасности.|
| роль | *AllDatabasesAdmin* | Роль основного кластера, которая может быть 'AllDatabasesAdmin' или 'AllDatabasesViewer'.|
| tenantIdForPrincipal | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор арендатора доверителя.|
| principalType | *Приложение* | Тип принципала, который может быть 'Пользователь', 'App', или 'Группа'|

## <a name="next-steps"></a>Следующие шаги

* [Добавление основ базы данных](database-principal-csharp.md)
