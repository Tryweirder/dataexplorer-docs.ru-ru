---
title: Настройка ключей, управляемых клиентом, в Azure обозреватель данных с помощью шаблона Azure Resource Manager
description: В этой статье описывается, как настроить шифрование ключей, управляемых клиентом, на основе данных в Azure обозреватель данных с помощью шаблона Azure Resource Manager.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: ad3351045f61f4fb9ddb9ce08c63201746ac05bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350236"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>Настройка ключей, управляемых клиентом, с помощью шаблона Azure Resource Manager

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Настройка шифрования с помощью управляемых клиентом ключей

В этом разделе вы настраиваете управляемые клиентом ключи с помощью шаблонов Azure Resource Manager. По умолчанию в Azure обозреватель данных Encryption используются ключи, управляемые корпорацией Майкрософт. На этом шаге настройте кластер Azure обозреватель данных для использования ключей, управляемых клиентом, и укажите ключ, связываемый с кластером.

Шаблон Azure Resource Manager можно развернуть с помощью портал Azure или с помощью PowerShell.

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

## <a name="update-the-key-version"></a>Обновление версии ключа

При создании новой версии ключа необходимо обновить кластер для использования новой версии. Сначала вызовите метод, `Get-AzKeyVaultKey` чтобы получить последнюю версию ключа. Затем обновите свойства хранилища ключей кластера, чтобы использовать новую версию ключа, как показано в разделе [Настройка шифрования с помощью управляемых клиентом ключей](#configure-encryption-with-customer-managed-keys).

## <a name="next-steps"></a>Дальнейшие действия

* [Защита кластеров Azure обозреватель данных в Azure](security.md)
* [Настройка управляемых удостоверений для кластера Azure обозреватель данных](managed-identities.md)
* [Защитите свой кластер в Azure обозреватель данных — портал Azure](manage-cluster-security.md) , включив шифрование неактивных компонентов.
* [Настройка ключей, управляемых клиентом, с помощью C #](customer-managed-keys-csharp.md)

