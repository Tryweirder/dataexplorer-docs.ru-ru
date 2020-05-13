---
title: Настройка управляемых удостоверений для кластера Azure обозреватель данных
description: Узнайте, как настроить управляемые удостоверения для кластера Azure обозреватель данных.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 523330f5ace4d9f2d652eccbd746b039d66df749
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374300"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Настройка управляемых удостоверений для кластера Azure обозреватель данных

[Управляемое удостоверение из Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) позволяет кластеру легко получать доступ к другим ресурсам, защищенным AAD, таким как Azure Key Vault. Удостоверение управляется платформой Azure и не требует предоставления или смены секретов. В этой статье показано, как создать управляемое удостоверение для кластеров Azure обозреватель данных. Конфигурация управляемого удостоверения в настоящее время поддерживается только для [включения управляемых клиентом ключей для кластера](security.md#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Управляемые удостоверения для Azure обозреватель данных не будут работать должным образом, если кластер Azure обозреватель данных переносится между подписками или клиентами. Приложению потребуется получить новое удостоверение, которое можно сделать, [отключив](#disable-a-system-assigned-identity) и [повторно включив](#add-a-system-assigned-identity) функцию. Кроме того, для использования нового удостоверения необходимо обновить политики доступа к нижестоящим ресурсам.

## <a name="add-a-system-assigned-identity"></a>Добавление удостоверения, назначенного системой
                                                                                                    
Назначьте назначенное системой удостоверение, привязанное к кластеру, и удаляется, если кластер удален. Кластер может иметь только одно назначенное системой удостоверение. Для создания кластера с удостоверением, назначенным системой, требуется задать дополнительное свойство в кластере. Назначенное системой удостоверение добавляется с помощью C#, шаблонов ARM или портал Azure, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Добавление удостоверения, назначенного системой, с помощью портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Новый кластер Azure обозреватель данных

1. [Создание кластера Azure обозреватель данных](create-cluster-database-portal.md#create-a-cluster) 
1. На вкладке **безопасность** > **назначенное системой удостоверение**выберите **вкл**. Чтобы удалить удостоверение, назначенное системой, выберите **выкл**.
2. Чтобы создать кластер, нажмите кнопку **Далее: теги>** или **Обзор + создать** .

    ![Добавление удостоверения, назначенного системой, в новый кластер](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Существующий кластер Azure обозреватель данных

1. Откройте существующий кластер Azure обозреватель данных.
1. **Settings**  >  На левой панели портала выберите параметры**удостоверение** .
1. На панели **удостоверение** > вкладке **назначено системой** :
   1. Переместите ползунок **состояния** в положение **вкл**.
   1. Нажмите кнопку **Сохранить**.
   1. Во всплывающем окне выберите **Да** .

    ![Добавить удостоверение, назначенное системой](media/managed-identities/turn-system-assigned-identity-on.png)

1. Через несколько минут на экране появится следующее: 
  * **Идентификатор объекта** , используемый для управляемых пользователем ключей 
  * **Назначение ролей** — щелкните ссылку, чтобы назначить соответствующие роли.

    ![Удостоверение, назначенное системой](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Добавление удостоверения, назначенного системой, с помощью языка C #

#### <a name="prerequisites"></a>Предварительные требования

Чтобы настроить управляемое удостоверение с помощью клиента Azure обозреватель данных C#:

* Установите [пакет NuGet для Azure обозреватель данных (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Установите [пакет NuGet Microsoft. IdentityModel. Clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) для проверки подлинности.
* [Создайте приложение Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) и субъект-службу, которые могут получать доступ к ресурсам. Вы добавляете назначение ролей в области подписки и получаете необходимые `Directory (tenant) ID` , `Application ID` и `Client Secret` .

#### <a name="create-or-update-your-cluster"></a>Создание или обновление кластера

1. Создайте или обновите кластер с помощью `Identity` Свойства:

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
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Выполните следующую команду, чтобы проверить успешность создания или обновления кластера с помощью удостоверения.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Если результат содержит `ProvisioningState` `Succeeded` значение, то кластер был создан или обновлен и должен иметь следующие свойства:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`и `TenantId` заменяются идентификаторами GUID. `TenantId`Свойство определяет клиент AAD, которому принадлежит удостоверение. `PrincipalId`— Это уникальный идентификатор для нового удостоверения кластера. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.

# <a name="arm-template"></a>[Шаблон ARM](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Добавление удостоверения, назначенного системой, с помощью шаблона Azure Resource Manager

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в Azure обозреватель данных см. в статье [Создание кластера и базы данных azure обозреватель данных с помощью шаблона Azure Resource Manager](create-cluster-database-resource-manager.md).

Добавление назначенного системой типа указывает Azure создавать удостоверение кластера и управлять им. Любой ресурс типа `Microsoft.Kusto/clusters` можно создать с помощью удостоверения, добавив следующее свойство в определение ресурса: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Пример:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

При создании кластера он имеет следующие дополнительные свойства:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`и `<PRINCIPALID>` заменяются идентификаторами GUID. `TenantId`Свойство определяет клиент AAD, которому принадлежит удостоверение. `PrincipalId`— Это уникальный идентификатор для нового удостоверения кластера. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.

---

## <a name="disable-a-system-assigned-identity"></a>Отключение назначенного системой удостоверения

Удаление назначенного системой удостоверения также приведет к его удалению из AAD. Назначенные системой удостоверения также автоматически удаляются из AAD при удалении ресурса кластера. Чтобы удалить назначенное системой удостоверение, отключите эту функцию.  Назначенное системой удостоверение удаляется с помощью C#, шаблонов ARM или портал Azure, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Отключение назначенного системой удостоверения с помощью портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
1. **Settings**  >  На левой панели портала выберите параметры**удостоверение** .
1. На панели **удостоверение** > вкладке **назначено системой** :
    1. Переместите ползунок **состояния** в **положение выкл**.
    1. Нажмите кнопку **Сохранить**.
    1. Во всплывающем окне выберите **Да** , чтобы отключить назначенное системой удостоверение. Панель **удостоверений** возвращается к тому же условию, что и перед добавлением удостоверения, назначенного системой.

    ![Удостоверение, назначенное системой, отключено](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Удаление назначенного системой удостоверения с помощью C #

Чтобы удалить назначенное системой удостоверение, выполните следующую команду:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Шаблон ARM](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Удаление назначенного системой удостоверения с помощью шаблона Azure Resource Manager

Чтобы удалить назначенное системой удостоверение, выполните следующую команду:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Следующие шаги

* [Защита кластеров Azure обозреватель данных в Azure](security.md)
* [Защитите свой кластер в Azure обозреватель данных — портал Azure](manage-cluster-security.md) , включив шифрование неактивных компонентов.
 * [Настройка ключей, управляемых клиентом, с помощью C #](customer-managed-keys-csharp.md)
 * [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](customer-managed-keys-resource-manager.md)
