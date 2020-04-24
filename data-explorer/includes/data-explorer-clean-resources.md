---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: de8a37b1b27dc1bff377c6212c0bfea0a13c7de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496489"
---
## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Очистка ресурсов с помощью портала Azure

Удалите ресурсы на портале Azure, выяснив следующие действия по [очистке ресурсов.](../create-cluster-database-portal.md#clean-up-resources)

### <a name="clean-up-resources-using-powershell"></a>Очистка ресурсов с помощью PowerShell

Если облачная оболочка по-прежнему открыта, вам не нужно копировать/запускать первую строку (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```