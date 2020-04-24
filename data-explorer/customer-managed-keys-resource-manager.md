---
title: Настройка ключей, управляемых клиентами, в Explorer данных Azure с помощью шаблона управления ресурсами Azure
description: В этой статье описывается, как настроить шифрование ключей, управляемых клиентом, на данных в Azure Data Explorer с помощью шаблона Azure Resource Manager.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 1af7404a20c7246b50f80385f666da886f0cc96b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496970"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Настройка управляемых клиентами ключей с помощью шаблона управления ресурсами Azure

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью ключей, управляемых клиентом

В этом разделе вы настраиваете управляемые клиентом ключи с помощью шаблонов Azure Resource Manager. По умолчанию шифрование Azure Data Explorer использует управляемые Корпорацией Майкрософт ключи. На этом этапе наймина кластер Azure Data Explorer для использования ключей, управляемых клиентами, и укажите ключ для ассоциированного с кластером.

Шаблон управления ресурсами Azure можно развернуть с помощью портала Azure или с помощью PowerShell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>Обновление ключевой версии

При создании новой версии ключа необходимо обновить кластер, чтобы использовать новую версию. Во-первых, позвоните, `Get-AzKeyVaultKey` чтобы получить последнюю версию ключа. Затем обновите свойства ключа хранилища кластера, чтобы использовать новую версию ключа, как показано в [шифровании Настройки с ключами, управляемыми клиентом.](#configure-encryption-with-customer-managed-keys)

## <a name="next-steps"></a>Следующие шаги

* [Защита кластеров Исследователей данных Azure в Azure](security.md)
* [Настройка управляемых идентификаторов для кластера Azure Data Explorer](managed-identities.md)
* [Защитите свой кластер на портале Azure Data Explorer - Azure,](manage-cluster-security.md) включив шифрование в состоянии покоя.
* [Настройка управляемых клиентами ключей с помощью C #](customer-managed-keys-csharp.md)

