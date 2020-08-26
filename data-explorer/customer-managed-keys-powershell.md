---
title: Настройка ключей, управляемых клиентом, с помощью PowerShell
description: В этой статье описывается, как настроить шифрование ключей, управляемых клиентом, на основе данных в Azure обозреватель данных с помощью PowerShell.
author: orspod
ms.author: orspodek
ms.reviewer: roshauli
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/04/2020
ms.openlocfilehash: 5c52253a6d8c4978931aeff3a5a5a73367f14ce5
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872001"
---
# <a name="configure-customer-managed-keys-using-powershell"></a>Настройка ключей, управляемых клиентом, с помощью PowerShell

> [!div class="op_single_selector"]
> * [Портал](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Шаблон Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-powershell"></a>Включение шифрования с использованием управляемых клиентом ключей с помощью PowerShell

В этой статье показано, как включить шифрование ключей, управляемых клиентом, с помощью PowerShell. По умолчанию в Azure обозреватель данных Encryption используются ключи, управляемые корпорацией Майкрософт. Настройте кластер Azure обозреватель данных для использования ключей, управляемых клиентом, и укажите ключ, связываемый с кластером.

1. Выполните следующую команду, чтобы войти в Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Задайте подписку, в которой зарегистрирован кластер.

    ```azurepowershell-interactive
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

1. Выполните следующую команду, чтобы задать новый ключ.

    ```azurepowershell-interactive
    Update-AzKustoCluster -ResourceGroupName "mytestrg" -Name "mytestcluster" -KeyVaultPropertyKeyName "<key-name>" -KeyVaultPropertyKeyVaultUri "<key-vault-uri>" -KeyVaultPropertyKeyVersion "<key-version>"
    ```

1. Выполните следующую команду и проверьте "Кэйваултпроперти..." свойства для проверки успешного обновления кластера.

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name "mytestcluster" -ResourceGroupName "mytestrg" | Format-List
    ```
