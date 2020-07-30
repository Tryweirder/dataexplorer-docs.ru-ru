---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: orspodek
ms.openlocfilehash: d55077b5d1938caf6df49d34e68ece7e32c56f85
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350079"
---
## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Очистка ресурсов с помощью портал Azure

Удалите ресурсы в портал Azure, выполнив действия, описанные в разделе [Очистка ресурсов](../create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Очистка ресурсов с помощью PowerShell

Если вы не закрывали Cloud Shell, вам не нужно копировать или выполнять первую строку (для чтения и размещения).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```