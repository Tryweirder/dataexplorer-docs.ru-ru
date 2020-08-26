---
title: Включение шифрования инфраструктуры (двойное шифрование) во время создания кластера в Azure обозреватель данных
description: В этой статье описывается, как включить шифрование инфраструктуры (двойное шифрование) во время создания кластера в обозреватель данных Azure.
author: orspod
ms.author: orspodek
ms.reviewer: toleibov
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/11/2020
ms.openlocfilehash: e89ce6f77545b4f0b42cbb3d792edd5ceeb0ed34
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874683"
---
# <a name="enable-infrastructure-encryption-double-encryption-during-cluster-creation-in-azure-data-explorer"></a>Включение шифрования инфраструктуры (двойное шифрование) во время создания кластера в Azure обозреватель данных
  
При создании кластера его хранилище [автоматически шифруется на уровне обслуживания](/azure/storage/common/storage-service-encryption). Если требуется более высокий уровень гарантии безопасности данных, можно также включить [Шифрование на уровне инфраструктуры службы хранилища Azure](/azure/storage/common/infrastructure-encryption-enable), также называемое двойным шифрованием. При включении шифрования инфраструктуры данные в учетной записи хранения шифруются дважды, один раз на уровне службы и один раз на уровне инфраструктуры, используя два разных алгоритма шифрования и два разных ключа. Двойное шифрование данных службы хранилища Azure позволяет защититься от ситуации, когда один из алгоритмов шифрования или ключей может быть скомпрометирован. В этом сценарии дополнительный уровень шифрования сохраняется для защиты данных.

> [!IMPORTANT]
> * Включение двойного шифрования возможно только при создании кластера.
> * После включения шифрования инфраструктуры в кластере его **нельзя** отключить.
> * Двойное шифрование доступно только в регионах, где поддерживается шифрование инфраструктуры. Дополнительные сведения см. в разделе [Шифрование инфраструктуры хранилища](/azure/storage/common/infrastructure-encryption-enable).

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

1. [Создание кластера Azure обозреватель данных](create-cluster-database-portal.md#create-a-cluster) 
1. На вкладке **безопасность** > **включить двойное шифрование**, выберите **вкл**. Чтобы удалить двойное шифрование, установите флажок **выкл**.
1. Чтобы создать кластер, нажмите кнопку **Далее: сетевая>** или **Обзор + создать** .

    :::image type="content" source="media/double-encryption/double-encryption-portal.png" alt-text="двойное шифрование нового кластера":::


# <a name="c"></a>[C#](#tab/c-sharp)

Вы можете включить шифрование инфраструктуры во время создания кластера с помощью C#.

## <a name="prerequisites"></a>Предварительные условия

Настройка управляемого удостоверения с помощью клиента Azure обозреватель данных C#:

* Установите [пакет NuGet для Azure обозреватель данных (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [пакет NuGet Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) для проверки подлинности.
* [Создайте приложение Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) и субъект-службу, которые могут получать доступ к ресурсам. Вы добавляете назначение ролей в области подписки и получаете необходимые `Directory (tenant) ID` , `Application ID` и `Client Secret` .

## <a name="create-your-cluster"></a>Создание кластера

1. Создайте кластер, используя `enableDoubleEncryption` свойство:

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
    var location = "East US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var enableDoubleEncryption = true;
    var cluster = new Cluster(location, sku, enableDoubleEncryption: enableDoubleEncryption);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
1. Выполните следующую команду, чтобы проверить успешность создания кластера:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Если результат содержит `ProvisioningState` `Succeeded` значение, то кластер был успешно создан.

# <a name="arm-template"></a>[Шаблон ARM](#tab/arm)

Шифрование инфраструктуры можно включить во время создания кластера с помощью Azure Resource Manager.

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в Azure обозреватель данных см. в статье [Создание кластера и базы данных azure обозреватель данных с помощью шаблона Azure Resource Manager](create-cluster-database-resource-manager.md).

## <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Добавление удостоверения, назначенного системой, с помощью шаблона Azure Resource Manager

1. Добавьте тип "Енабледаублинкриптион", чтобы сообщить Azure включить шифрование инфраструктуры (двойное шифрование) для кластера.
    
    ```json
    {
        "apiVersion": "2020-06-14",
        "type": "Microsoft.Kusto/clusters",
        "name": "[variables('clusterName')]",
        "location": "[resourceGroup().location]",
        "properties": {
            "trustedExternalTenants": [],
            "virtualNetworkConfiguration": null,
            "optimizedAutoscale": null,
            "enableDiskEncryption": false,
            "enableStreamingIngest": false,
            "enableDoubleEncryption": true,
        }
    }
    ```

1. При создании кластера он имеет следующие дополнительные свойства:

    ```json
    "identity": {
        "type": "SystemAssigned",
        "tenantId": "<TENANTID>",
        "principalId": "<PRINCIPALID>"
    }
    ```
---

## <a name="next-steps"></a>Дальнейшие шаги

[Проверка работоспособности кластера](check-cluster-health.md)
