---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496996"
---
## <a name="create-a-new-key-vault"></a>Создание нового хранилища ключей

Чтобы создать новый хранилище ключей с помощью PowerShell, [позвоните в New-AzKeyVault.](/powershell/module/az.keyvault/new-azkeyvault) Ключевое хранилище, которое используется для хранения ключей, управляемых клиентами для шифрования Azure Data Explorer, должно иметь два ключевых настройки защиты, **Soft Delete** и Do **Not Purge.** Замените значения заполнителя в скобках своими собственными значениями, приведенными ниже.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа к хранилищу ключей

Затем наверсните политику доступа для хранилища ключей таким образом, чтобы кластер получил разрешения на доступ к нему. На этом этапе вы будете использовать назначенную системой управляемую идентификацию, которую вы ранее назначили кластеру. Чтобы установить политику доступа для хранилища ключей, позвоните [set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Замените значения заполнителя в скобках на собственные значения и используйте переменные, определенные в предыдущих примерах.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Создание ключа

Затем создайте новый ключ в хранилище ключей. Чтобы создать новый ключ, позвоните [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Замените значения заполнителя в скобках на собственные значения и используйте переменные, определенные в предыдущих примерах.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
