---
title: 'Добавление основ базы данных для Azure Data Explorer с помощью C #'
description: В этой статье вы узнаете, как добавить принципы базы данных для Azure Data Explorer с помощью C.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 17107c1164b0ff84600776afd32feb4ef40256ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498673"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Добавление основ базы данных для Azure Data Explorer с помощью C #

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Шаблон Azure Resource Manager](database-principal-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы добавляете принципы базы данных для Azure Data Explorer с помощью C'.

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что вы **включите разработку Azure** во время установки Visual Studio.
* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Создание кластера и базы данных](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Установка СЗ NuGet

* Установка [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) для проверки подлинности.

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Добавление основной базы данных

Следующий пример показывает, как добавить основную базу данных программно.

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
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Параметр** | **Рекомендуемое значение** | **Описание поля**|
|---|---|---|
| tenantId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента. Также известен как идентификатор каталога.|
| subscriptionId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор подписки, который используется для создания ресурсов.|
| clientid | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор клиента приложения, который может получить доступ к ресурсам в вашем арендаторе.|
| clientSecret | *xxxxxxxxxxxxxxxxxxxx* | Секрет клиента приложения, которое может получить доступ к ресурсам в вашем арендаторе. |
| имя_группы_ресурсов | *testrg* | Название группы ресурсов, содержащей кластер.|
| clusterName | *mykustocluster* | Название кластера.|
| databaseName | *mykustodatabase* | Имя базы данных.|
| mainAssignmentName | *База данныхПринципиальноеназначение1* | Имя основного ресурса базы данных.|
| principalId | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Основным идентификатором, который может быть электронной почтой пользователя, идентификатором приложения или именем группы безопасности.|
| роль | *Администрирование* | Роль основного специалиста базы данных, которая может быть 'Админ', 'Ingestor', 'Монитор', 'Пользователь', 'Неограниченные Зрители', 'Viewer'.|
| tenantIdForPrincipal | *xxxxxxxxxx-xxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxx* | Идентификатор арендатора доверителя.|
| principalType | *Приложение* | Тип принципала, который может быть 'Пользователь', 'App', или 'Группа'|

## <a name="next-steps"></a>Следующие шаги

* [Проем данных с помощью библиотеки узлов Azure Data Explorer](node-ingest-data.md)
