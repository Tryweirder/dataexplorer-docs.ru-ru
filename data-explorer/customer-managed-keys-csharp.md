---
title: 'Настройка ключей, управляемых клиентом, с помощью C #'
description: В этой статье описывается, как настроить ключи, управляемые клиентом, для шифрования данных Azure обозреватель данных с помощью C#.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: db20566a9aa9b5c720ea9f72ec9c980042db0625
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92003012"
---
# <a name="configure-customer-managed-keys-using-c"></a>Настройка ключей, управляемых клиентом, с помощью C #

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с использованием ключей, управляемых клиентом

В этом разделе показано, как настроить шифрование ключей, управляемых клиентом, с помощью клиента Azure обозреватель данных C#. 

### <a name="prerequisites"></a>Предварительные условия

* Если вы еще не установили Visual Studio 2019, вы можете скачать и использовать **бесплатный** [выпуск Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). При установке Visual Studio необходимо включить возможность **разработки для Azure**.

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

### <a name="install-c-nuget"></a>Установка пакета NuGet для C#

* Установите [пакет NuGet для Azure обозреватель данных (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Установите [пакет NuGet Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) для проверки подлинности.

### <a name="authentication"></a>Аутентификация

Чтобы выполнить примеры в этой статье, [Создайте приложение Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) и субъект-службу, которые могут получать доступ к ресурсам. Можно добавить назначение ролей в области подписки и получить необходимые `Directory (tenant) ID` , `Application ID` и `Client Secret` .

### <a name="configure-cluster"></a>Настройка кластера

По умолчанию в Azure обозреватель данных Encryption используются ключи, управляемые корпорацией Майкрософт. Настройте кластер Azure обозреватель данных для использования ключей, управляемых клиентом, и укажите ключ, связываемый с кластером.

1. Обновите кластер, используя следующий код:

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

1. Выполните следующую команду, чтобы проверить успешность обновления кластера:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Если результат содержит `ProvisioningState` `Succeeded` значение, значит, кластер успешно обновлен.

## <a name="update-the-key-version"></a>Обновление версии ключа

При создании новой версии ключа необходимо обновить кластер для использования новой версии. Сначала вызовите метод, `Get-AzKeyVaultKey` чтобы получить последнюю версию ключа. Затем обновите свойства хранилища ключей кластера, чтобы использовать новую версию ключа, как показано в разделе [Настройка кластера](#configure-cluster).

## <a name="next-steps"></a>Дальнейшие действия

* [Защита кластеров Azure обозреватель данных в Azure](security.md)
* [Настройка управляемых удостоверений для кластера Azure обозреватель данных](managed-identities.md)
* [Защитите свой кластер с помощью шифрования дисков в Azure обозреватель данных — портал Azure](cluster-disk-encryption.md) , включив шифрование неактивных компонентов.
* [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](customer-managed-keys-resource-manager.md)


