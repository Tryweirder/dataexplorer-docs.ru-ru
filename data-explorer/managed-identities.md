---
title: Как настроить управляемые идентификаторы для кластера Azure Data Explorer
description: Узнайте, как настроить управляемые идентификаторы для кластера Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 900bf815917a4b7c9841860d663a2183b1ab71b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81499466"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Настройка управляемых идентификаторов для кластера Azure Data Explorer

[Управляемый итог из каталога Azure Active](/azure/active-directory/managed-identities-azure-resources/overview) позволяет кластеру легко получить доступ к другим защищенным AAD ресурсам, таким как Azure Key Vault. Идентификация управляется платформой Azure и не требует предоставления или ротации каких-либо секретов. В этой статье показано, как создать управляемый имитатор для кластеров Azure Data Explorer. Управляемая конфигурация идентификации в настоящее время поддерживается только для [включения ключей, управляемых клиентом для вашего кластера.](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)

> [!Note]
> Управляемые идентификаторы для Azure Data Explorer не будут вести себя так, как ожидалось, если кластер Azure Data Explorer будет перенесен через подписки или арендаторов. Приложение должно будет получить новую личность, что может быть сделано путем [отключения](#disable-a-system-assigned-identity) и [повторного включения](#add-a-system-assigned-identity) функции. Политики доступа к ресурсам ниже по течению также необходимо будет обновить для использования новой идентификации.

## <a name="add-a-system-assigned-identity"></a>Добавление установленного системой удостоверения
                                                                                                    
Назначайте присвоив аименное удостоверение, назначенное системой, которое привязано к кластеру и удаляется при удалении кластера. Кластер может иметь только одно установленное системой удостоверение личности. Создание кластера с установленным системой удостоверением требует дополнительного свойства, который будет установлен на кластере. Идентификационная информация, назначенная системой, добавляется с помощью шаблонов СЗ, ARM или портала Azure, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Добавление системного удостоверения с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Новый кластер исследователей данных Azure

1. [Создание кластера исследователя данных Azure](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. На вкладке **security** > **системы присваивается идентификационный данный,** выберите **On**. Чтобы удалить назначенную систему идентификацию, выберите **Off**.
2. Выберите **следующий:Теги>** или **обзор и создать** для создания кластера.

    ![Добавление системы, назначенной удостоверением в новый кластер](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Существующий кластер исследователя данных Azure

1. Откройте существующий кластер Azure Data Explorer.
1. Выберите**идентификацию** **настроек** > в левом стеле портала.
1. В системе **> идентификационных** > **назначена** вкладка:
   1. Переместите ползунок **Status** **on**.
   1. Нажмите кнопку **Сохранить**.
   1. Во всплывающем окне выберите **«Да»**

    ![Добавление системы назначенного удостоверения](media/managed-identities/turn-system-assigned-identity-on.png)

1. Через несколько минут на экране видно: 
  * **Идентификатор объекта** - используется для ключей управления клиентом 
  * **Назначения ролей** - нажмите ссылку, чтобы назначить соответствующие роли

    ![Система назначена личность на](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Добавление системного удостоверения с помощью C #

#### <a name="prerequisites"></a>Предварительные требования

Настройка управляемой идентификации с помощью клиента Azure Data Explorer C:

* Установите [пакет Azure Data Explorer (Kusto) NuGet.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Установите [пакет Microsoft.IdentityModel.Customers.ActiveDirectory NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) для проверки подлинности.
* [Создайте приложение Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) и принцип обслуживания, который может получить доступ к ресурсам. Вы добавляете назначение ролей в области `Directory (tenant) ID` `Application ID`подписки `Client Secret`и получаете требуемое, и .

#### <a name="create-or-update-your-cluster"></a>Создание или обновление кластера

1. Создайте или обновите кластер с помощью `Identity` свойства:

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
    
2. Выполнить следующую команду, чтобы проверить, был ли успешно создан или обновлен кластер с помощью идентификации:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Если результат `ProvisioningState` содержит `Succeeded` значение, то кластер был создан или обновлен и должен иметь следующие свойства:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`и `TenantId` заменяются GUID. Свойство `TenantId` идентифицирует арендатора AAD, к которому принадлежит личность. Это `PrincipalId` уникальный идентификатор для новой идентичности кластера. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.

# <a name="arm-template"></a>[Шаблон ARM](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Добавление системного удостоверения с помощью шаблона управления ресурсами Azure

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Чтобы узнать больше о развертывании в Azure Data Explorer, [см.](create-cluster-database-resource-manager.md)

Добавление заданного в системе типа говорит Azure о создании и управлении идентификатором для кластера. Любой ресурс типа `Microsoft.Kusto/clusters` можно создать с помощью удостоверения, добавив следующее свойство в определение ресурса: 

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

`<TENANTID>`и `<PRINCIPALID>` заменяются GUID. Свойство `TenantId` идентифицирует арендатора AAD, к которому принадлежит личность. Это `PrincipalId` уникальный идентификатор для новой идентичности кластера. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.

---

## <a name="disable-a-system-assigned-identity"></a>Отключить установленное системой удостоверение личности

Удаление установленного системой удостоверения также удалит его из AAD. Системные идентификаторы также автоматически удаляются из AAD при удалении ресурса кластера. Удостоверение, назначенное системой, может быть удалено путем отключения функции.  Удостоверение, назначенное системой, удаляется с помощью шаблонов C', ARM или портала Azure, как описано ниже.

# <a name="azure-portal"></a>[Портал Azure](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Отогнаните системное удостоверение с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Выберите**идентификацию** **настроек** > в левом стеле портала.
1. В системе **> идентификационных** > **назначена** вкладка:
    1. Переместите слайдер **Status** в **Off**.
    1. Нажмите кнопку **Сохранить**.
    1. В всплывающем окне выберите **«Да»,** чтобы отключить установленную системой идентификацию. Панель **идентификации** возвращается в то же состояние, что и до добавления установленного системой удостоверения личности.

    ![Система назначена идентификация выключена](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Удаление установленного системой удостоверения с помощью C #

Выполнить следующее, чтобы удалить установленную систему:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Шаблон ARM](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Удалите системное удостоверение с помощью шаблона управления ресурсами Azure

Выполнить следующее, чтобы удалить установленную систему:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Следующие шаги

* [Защита кластеров Исследователей данных Azure в Azure](security.md)
* [Защитите свой кластер на портале Azure Data Explorer - Azure,](manage-cluster-security.md) включив шифрование в состоянии покоя.
 * [Настройка управляемых клиентами ключей с помощью C #](customer-managed-keys-csharp.md)
 * [Настройка управляемых клиентами ключей с помощью шаблона управления ресурсами Azure](customer-managed-keys-resource-manager.md)
