---
title: 'Настройка управляемых клиентами ключей с помощью C #'
description: В этой статье описывается, как настроить шифрование ключей, управляемых клиентом, на данных в Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: e394d928774624ac3c7faacab7726570272da82a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497490"
---
# <a name="configure-customer-managed-keys-using-c"></a>Настройка управляемых клиентами ключей с помощью C #

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью ключей, управляемых клиентом

В этом разделе показано, как настроить шифрование ключей, управляемых клиентом, с помощью клиента Azure Data Explorer C. 

### <a name="prerequisites"></a>Предварительные требования

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Убедитесь, что вы **включите разработку Azure** во время установки Visual Studio.

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

### <a name="install-c-nuget"></a>Установка СЗ NuGet

* Установите [пакет Azure Data Explorer (Kusto) NuGet.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)

* Установите [пакет Microsoft.IdentityModel.Customers.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) для проверки подлинности.

### <a name="authentication"></a>Аутентификация

Чтобы выполнить примеры в этой статье, [создайте приложение Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) и принцип службы, который может получить доступ к ресурсам. Вы можете добавить назначение ролей в `Directory (tenant) ID`области `Application ID`подписки и получить требуемое, и `Client Secret`.

### <a name="configure-cluster"></a>Настройка кластера

По умолчанию шифрование Azure Data Explorer использует управляемые Корпорацией Майкрософт ключи. Нанастройка кластера Azure Data Explorer для использования ключей, управляемых клиентами, и укажите ключ для ассоциированного с кластером.

1. Обновление кластера с помощью следующего кода:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
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
    var clusterName = "mykustocluster";
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Выполнить следующую команду, чтобы проверить, был ли успешно обновлен кластер:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Если результат `ProvisioningState` содержит `Succeeded` значение, то кластер был успешно обновлен.

## <a name="update-the-key-version"></a>Обновление ключевой версии

При создании новой версии ключа необходимо обновить кластер, чтобы использовать новую версию. Во-первых, позвоните, `Get-AzKeyVaultKey` чтобы получить последнюю версию ключа. Затем обновите свойства ключа хранилища кластера, чтобы использовать новую версию ключа, как показано в [кластере Настройка.](#configure-cluster)

## <a name="next-steps"></a>Следующие шаги

* [Защита кластеров Исследователей данных Azure в Azure](security.md)
* [Настройка управляемых идентификаторов для кластера Azure Data Explorer](managed-identities.md)
* [Защитите свой кластер на портале Azure Data Explorer - Azure,](manage-cluster-security.md) включив шифрование в состоянии покоя.
* [Настройка управляемых клиентами ключей с помощью шаблона управления ресурсами Azure](customer-managed-keys-resource-manager.md)


