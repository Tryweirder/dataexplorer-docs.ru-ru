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

Чтобы создать новое хранилище ключей с помощью PowerShell, вызовите [New-азкэйваулт](/powershell/module/az.keyvault/new-azkeyvault). Хранилище ключей, используемое для хранения ключей, управляемых клиентом, для шифрования Azure обозреватель данных должно включать два параметра защиты ключей: **обратимое удаление** и **не очищать**. Замените значения заполнителей в квадратных скобках собственными значениями в примере ниже.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа к хранилищу ключей

Затем настройте политику доступа для хранилища ключей, чтобы у кластера было разрешение на доступ к нему. На этом шаге вы будете использовать управляемое системой удостоверение, назначенное кластеру ранее. Чтобы задать политику доступа для хранилища ключей, вызовите [Set-азкэйваултакцессполици](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Замените значения заполнителей в квадратных скобках собственными значениями и используйте переменные, определенные в предыдущих примерах.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Создание ключа

Затем создайте новый ключ в хранилище ключей. Чтобы создать новый ключ, вызовите [Add-азкэйваулткэй](/powershell/module/az.keyvault/add-azkeyvaultkey). Замените значения заполнителей в квадратных скобках собственными значениями и используйте переменные, определенные в предыдущих примерах.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
