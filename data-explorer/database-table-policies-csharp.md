---
title: Создание политик с помощью SDK Исследователя данных Azure
description: В этой статье вы узнаете, как создавать политики с помощью C.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: ee9d740a3bd9748611b4e822f5204eee2633b1bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496892"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Создание политик базы данных и таблиц для Azure Data Explorer с помощью C #

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы создадите политики баз данных и таблиц для Azure Data Explorer с помощью C'.

## <a name="prerequisites"></a>Предварительные требования

* Visual Studio 2019. Если у вас нет Visual Studio 2019, вы можете скачать и использовать *бесплатный* [Visual Studio Community 2019](https://www.visualstudio.com/downloads/). Не забудьте выбрать **разработку Azure** во время установки Visual Studio.
* Подписка Azure. При необходимости можно создать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/) перед запуском.
* [Тестовый кластер и база данных](create-cluster-database-csharp.md).
* [Таблица тестирования](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Установка СЗ NuGet

* Установите [пакет Azure Data Explorer (Kusto) NuGet.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Установите [пакет Microsoft.Azure.Kusto.Data.NETStandard NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Необязательно для политики изменения таблицы.)
* Установите [пакет Microsoft.IdentityModel.Customers.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)для проверки подлинности.

## <a name="authentication"></a>Аутентификация
Для запуска примеров в этой статье требуется приложение Azure Active Directory (Azure AD) и принцип службы, который может получить доступ к ресурсам. Для проверки подлинности из [тестового кластера и базы данных](create-cluster-database-csharp.md#authentication)можно использовать то же приложение Azure AD. Если вы хотите использовать другое приложение Azure AD, см. [создать приложение Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) для создания бесплатного приложения Azure AD и добавления ролевого задания в области подписки. В этой статье также `Directory (tenant) ID`показано, как получить , `Application ID`, и `Client secret`. Возможно, потребуется добавить новое приложение Azure AD в качестве основного в базу данных. Для получения дополнительной [информации см.](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)

## <a name="alter-database-retention-policy"></a>Изменить политику хранения баз данных
Устанавливает политику удержания с 10-дневным периодом мягкого удаления.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Политика кэша кэша базы данных
Устанавливает политику кэша для базы данных. Предыдущие пять дней данные будут находиться на кластере SSD.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Изменение политики кэша таблиц
Устанавливает политику кэша для таблицы. Предыдущие пять дней данные будут находиться на кластере SSD.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Добавление нового принципала для базы данных
Добавляет новое приложение Azure AD в качестве основного средства для базы данных.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Следующие шаги

* [Подробнее о политиках баз данных и таблиц](kusto/management/policies.md)
