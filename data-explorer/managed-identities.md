---
title: Настройка управляемых удостоверений для кластера Azure обозреватель данных
description: Узнайте, как настроить управляемые удостоверения для кластера Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 2bfc2cd69d88395e04af16e732564fb90170ee7f
ms.sourcegitcommit: 7edce9d9d20f9c0505abda67bb8cc3d2ecd60d15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524289"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Настройка управляемых удостоверений для кластера Azure обозреватель данных

[Управляемое удостоверение из Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) позволяет кластеру легко получать доступ к другим ресурсам, защищенным Azure AD, таким как Azure Key Vault. Удостоверение управляется платформой Azure и не требует предоставления или смены секретов. Конфигурация управляемого удостоверения в настоящее время поддерживается только для [включения управляемых клиентом ключей для кластера](security.md#customer-managed-keys-with-azure-key-vault).

Кластеру Azure обозреватель данных могут быть предоставлены два типа удостоверений:

* **Назначенное системой удостоверение**: связано с кластером и удаляется, если ресурс удален. Кластер может иметь только одно назначенное системой удостоверение.
* **Назначаемое пользователем удостоверение**: автономный ресурс Azure, который можно назначить кластеру. Кластер может иметь несколько назначенных пользователю удостоверений.

В этой статье показано, как добавить и удалить назначенные системой и назначенные пользователем управляемые удостоверения для кластеров Azure обозреватель данных.

> [!Note]
> Управляемые удостоверения для Azure обозреватель данных не будут работать должным образом, если кластер Azure обозреватель данных переносится между подписками или клиентами. Приложению потребуется получить новое удостоверение, которое можно сделать, [отключив](#remove-a-system-assigned-identity) и [повторно включив](#add-a-system-assigned-identity) функцию. Кроме того, для использования нового удостоверения необходимо обновить политики доступа к нижестоящим ресурсам.

## <a name="add-a-system-assigned-identity"></a>Добавление назначаемого системой удостоверения

Назначьте назначенное системой удостоверение, привязанное к кластеру, и удаляется, если кластер удален. Кластер может иметь только одно назначенное системой удостоверение. Для создания кластера с удостоверением, назначенным системой, требуется задать дополнительное свойство в кластере. Добавьте удостоверение, назначенное системой, с помощью шаблона портал Azure, C# или диспетчер ресурсов, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Добавление удостоверения, назначенного системой, с помощью портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Новый кластер Azure обозреватель данных

1. [Создание кластера Azure обозреватель данных](create-cluster-database-portal.md#create-a-cluster) 
1. На вкладке **безопасность** > **назначенное системой удостоверение** выберите **вкл**. Чтобы удалить удостоверение, назначенное системой, выберите **выкл**.
1. Чтобы создать кластер, нажмите кнопку **Далее: теги >** или **Обзор + создать** .

    ![Добавление удостоверения, назначенного системой, в новый кластер](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Существующий кластер Azure обозреватель данных

1. Откройте существующий кластер Azure обозреватель данных.
1. **Settings**  >  На левой панели портала выберите параметры **удостоверение** .
1. На панели **удостоверение** > вкладке **назначено системой** :
   1. Переместите ползунок **состояния** в положение **вкл**.
   1. Нажмите кнопку **Сохранить**.
   1. Во всплывающем окне выберите **Да** .

    ![Добавить удостоверение, назначенное системой](media/managed-identities/turn-system-assigned-identity-on.png)

1. Через несколько минут на экране появится следующее:
    * **Идентификатор объекта** , используемый для управляемых клиентом ключей
    * **Разрешения** — выбор соответствующих назначений ролей

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

`PrincipalId` и `TenantId` заменяются идентификаторами GUID. `TenantId`Свойство определяет клиент Azure AD, которому принадлежит удостоверение. `PrincipalId`— Это уникальный идентификатор для нового удостоверения кластера. В Azure AD субъект-служба имеет то же имя, которое вы присвоили экземпляру Службы приложений или Функций Azure.

# <a name="resource-manager-template"></a>[Шаблон Resource Manager](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Добавление удостоверения, назначенного системой, с помощью шаблона Azure Resource Manager

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в Azure обозреватель данных см. в статье [Создание кластера и базы данных azure обозреватель данных с помощью шаблона Azure Resource Manager](create-cluster-database-resource-manager.md).

Добавление назначенного системой типа указывает Azure создавать удостоверение кластера и управлять им. Любой ресурс типа `Microsoft.Kusto/clusters` можно создать с помощью удостоверения, добавив следующее свойство в определение ресурса:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Пример.

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    }
}
```

> [!NOTE]
> В кластере одновременно могут быть назначены как системные, так и назначенные пользователем удостоверения. `type`Свойство будет`SystemAssigned,UserAssigned`

При создании кластера он имеет следующие дополнительные свойства:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` и `<PRINCIPALID>` заменяются идентификаторами GUID. `TenantId`Свойство определяет клиент Azure AD, которому принадлежит удостоверение. `PrincipalId`— Это уникальный идентификатор для нового удостоверения кластера. В Azure AD субъект-служба имеет то же имя, которое вы присвоили экземпляру Службы приложений или Функций Azure.

---

## <a name="remove-a-system-assigned-identity"></a>Удаление назначенного системой удостоверения

Удаление назначенного системой удостоверения также приведет к его удалению из Azure AD. Назначенные системой удостоверения также автоматически удаляются из Azure AD при удалении ресурса кластера. Чтобы удалить назначенное системой удостоверение, отключите эту функцию. Удалите назначенное системой удостоверение с помощью шаблона портал Azure, C# или диспетчер ресурсов, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>Удаление назначенного системой удостоверения с помощью портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
1. **Settings**  >  На левой панели портала выберите параметры **удостоверение** .
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

# <a name="resource-manager-template"></a>[Шаблон Resource Manager](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Удаление назначенного системой удостоверения с помощью шаблона Azure Resource Manager

Чтобы удалить назначенное системой удостоверение, выполните следующую команду:

```json
"identity": {
    "type": "None"
}
```

> [!NOTE]
> Если в кластере одновременно были назначены как системные, так и назначенные пользователем удостоверения, то после удаления удостоверения, назначенного системой, `type` свойство будет иметь значение. `UserAssigned`

---

## <a name="add-a-user-assigned-identity"></a>Добавление назначаемого пользователем удостоверения

Назначьте кластеру управляемое удостоверение, назначенное пользователем. Кластер может иметь более одного назначенного пользователю удостоверения. Для создания кластера с назначенным пользователем удостоверением требуется задать дополнительное свойство в кластере. Добавьте назначаемое пользователем удостоверение с помощью шаблона портал Azure, C# или диспетчер ресурсов, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="add-a-user-assigned-identity-using-the-azure-portal"></a>Добавление удостоверения, назначенного пользователем, с помощью портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
1. [Создание назначенного пользователем управляемого ресурса удостоверений](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal#create-a-user-assigned-managed-identity).
1. Откройте существующий кластер Azure обозреватель данных.
1. **Settings**  >  На левой панели портала выберите параметры **удостоверение** .
1. На вкладке **Назначенные пользователи** выберите **Добавить**.
1. Найдите созданное ранее удостоверение и выберите его. Выберите **Добавить**.

    ![Добавить удостоверение, назначенное пользователем](media/managed-identities/user-assigned-identity-select.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-user-assigned-identity-using-c"></a>Добавление назначенного пользователем удостоверения с помощью языка C #

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
    var identityName = "myIdentity";
    var userIdentityResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}";
    var userAssignedIdentities = new Dictionary<string, IdentityUserAssignedIdentitiesValue>(1) { { userIdentityResourceId, new IdentityUserAssignedIdentitiesValue() } };
    var identity = new Identity(type: IdentityType.UserAssigned, userAssignedIdentities: userAssignedIdentities);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

2. Выполните следующую команду, чтобы проверить успешность создания или обновления кластера с помощью удостоверения.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Если результат содержит `ProvisioningState` `Succeeded` значение, то кластер был создан или обновлен и должен иметь следующие свойства:

    ```csharp
    var userIdentity = cluster.Identity.UserAssignedIdentities[userIdentityResourceId];
    var principalId = userIdentity.PrincipalId;
    var clientId = userIdentity.ClientId;
    ```

`PrincipalId`— Это уникальный идентификатор для удостоверения, используемого для администрирования Azure AD. `ClientId`— Это уникальный идентификатор для нового удостоверения приложения, который используется для указания того, какое удостоверение следует использовать во время вызовов среды выполнения.

# <a name="resource-manager-template"></a>[Шаблон Resource Manager](#tab/arm)

### <a name="add-a-user-assigned-identity-using-an-azure-resource-manager-template"></a>Добавление назначенного пользователем удостоверения с помощью шаблона Azure Resource Manager

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в Azure обозреватель данных см. в статье [Создание кластера и базы данных azure обозреватель данных с помощью шаблона Azure Resource Manager](create-cluster-database-resource-manager.md).

Любой ресурс типа `Microsoft.Kusto/clusters` можно создать с пользовательским удостоверением, включив в определение ресурса следующее свойство, заменив `<RESOURCEID>` идентификатором ресурса требуемого удостоверения:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Пример.

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

При создании кластера он имеет следующие дополнительные свойства:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`PrincipalId`— Это уникальный идентификатор для удостоверения, используемого для администрирования Azure AD. `ClientId`— Это уникальный идентификатор для нового удостоверения приложения, который используется для указания того, какое удостоверение следует использовать во время вызовов среды выполнения.

> [!NOTE]
> В кластере одновременно могут быть назначены как системные, так и назначенные пользователем удостоверения. В этом случае свойство будет `type` иметь значение `SystemAssigned,UserAssigned` .

---

## <a name="remove-a-user-assigned-managed-identity-from-a-cluster"></a>Удаление назначенного пользователем управляемого удостоверения из кластера

Удалите назначенное пользователем удостоверение с помощью шаблона портал Azure, C# или диспетчер ресурсов, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="remove-a-user-assigned-managed-identity-using-the-azure-portal"></a>Удаление назначенного пользователем управляемого удостоверения с помощью портал Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
1. **Settings**  >  На левой панели портала выберите параметры **удостоверение** .
1. Выберите вкладку **назначенные пользователем** .
1. Найдите созданное ранее удостоверение и выберите его. Щелкните **Удалить**.

    ![Удалить назначенное пользователем удостоверение](media/managed-identities/user-assigned-identity-remove.png)

1. Во всплывающем окне выберите **Да** , чтобы удалить назначенное пользователю удостоверение. Панель **удостоверений** возвращается к тому же условию, что и перед добавлением удостоверения, назначенного пользователем.

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-user-assigned-identity-using-c"></a>Удаление назначенного пользователем удостоверения с помощью языка C #

Выполните следующую команду, чтобы удалить назначенное пользователю удостоверение:

```csharp
var identityName = "myIdentity";
var userIdentityResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}";
var userAssignedIdentities = new Dictionary<string, IdentityUserAssignedIdentitiesValue>(1) { { userIdentityResourceId, null } };
var identity = new Identity(type: IdentityType.UserAssigned, userAssignedIdentities: userAssignedIdentities);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="resource-manager-template"></a>[Шаблон Resource Manager](#tab/arm)

### <a name="remove-a-user-assigned-identity-using-an-azure-resource-manager-template"></a>Удаление назначенного пользователем удостоверения с помощью шаблона Azure Resource Manager

Выполните следующую команду, чтобы удалить назначенное пользователю удостоверение:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": null
    }
}
```

> [!NOTE]
>
> * Чтобы удалить удостоверения, присвойте их значения NULL. Все остальные существующие удостоверения не будут затронуты.
> * Чтобы удалить все назначенные пользователем удостоверения `type` , это свойство будет иметь значение. `None`
> * Если в кластере одновременно содержались как системные, так и назначенные пользователем удостоверения, `type` свойство будет иметь `SystemAssigned,UserAssigned` идентификаторы для удаления или `SystemAssigned` для удаления всех удостоверений, назначенных пользователем.

---

## <a name="next-steps"></a>Дальнейшие действия

* [Защита кластеров Azure обозреватель данных в Azure](security.md)
* [Защитите свой кластер с помощью шифрования дисков в Azure обозреватель данных — портал Azure](cluster-disk-encryption.md) , включив шифрование неактивных компонентов.
* [Настройка ключей, управляемых клиентом, с помощью C #](customer-managed-keys-csharp.md)
* [Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager](customer-managed-keys-resource-manager.md)
