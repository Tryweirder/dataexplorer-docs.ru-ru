---
title: Создание кластера и базы данных Azure обозреватель данных с помощью шаблона Azure Resource Manager
description: Узнайте, как создать кластер и базу данных Azure обозреватель данных с помощью шаблона Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/26/2019
ms.openlocfilehash: 40cd53d1bff6b33b81878c85c6c22c3fb85655ee
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902575"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Создание кластера и базы данных Azure обозреватель данных с помощью шаблона Azure Resource Manager

> [!div class="op_single_selector"]
> * [Портал](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [GO](create-cluster-database-go.md)
> * [Шаблон Azure Resource Manager](create-cluster-database-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы должны принять (загрузить) данные в базы данных, чтобы к ним можно было выполнять запросы. 

В этой статье вы создадите кластер и базу данных Azure обозреватель данных с помощью [шаблона Azure Resource Manager](/azure/azure-resource-manager/management/overview). Здесь показано, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Сведения о создании шаблонов см. в разделе [authoring Azure Resource Manager Templates](/azure/azure-resource-manager/resource-group-authoring-templates). Синтаксис и свойства JSON, используемые в шаблоне, см. в разделе [типы ресурсов Microsoft. Kusto](/azure/templates/microsoft.kusto/allversions).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Шаблон Azure Resource Manager для создания кластера и базы данных

В этой статье вы используете [существующий шаблон краткого руководства](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
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
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

См. [примеры шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Развертывание шаблона и проверка развертывания шаблона

Шаблон Azure Resource Manager можно развернуть с [помощью портал Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) или [с помощью PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Развертывание шаблона и проверка развертывания шаблона с помощью портал Azure

1. Чтобы создать кластер и базу данных, используйте следующую кнопку для запуска развертывания. Чтобы закончить выполнение инструкций из этой статьи, щелкните правой кнопкой мыши и выберите ссылку **Открыть в новом окне** .

    [![Снимок экрана с синей кнопкой, которая используется для изображений облаков и помечена как развернутая в Azure.](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Кнопка **Развернуть в Azure** выполняет переход на портал Azure для заполнения формы развертывания.

    :::image type="content" source="media/create-cluster-database-resource-manager/deploy-2-azure.png" alt-text="Снимок экрана шаблона на портал Azure. Все кнопки, флажки и флажки, используемые при редактировании, выделяются." border="false":::

    Шаблон можно [изменить и развернуть в портал Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) с помощью формы.

1. Выполните **Основные сведения** и разделы о **параметрах** . Выберите уникальные имена кластера и базы данных.
Создание кластера Azure обозреватель данных и базы данных занимает несколько минут.

1. Чтобы проверить развертывание, откройте группу ресурсов в [портал Azure](https://portal.azure.com) , чтобы найти новый кластер и базу данных. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Развертывание шаблона и проверка развертывания шаблона с помощью PowerShell

#### <a name="deploy-the-template-using-powershell"></a>Развертывание шаблона с помощью PowerShell

1. Выберите вариант **попробовать** в следующем блоке кода, а затем следуйте инструкциям по входу в Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Нажмите кнопку **Копировать** , чтобы скопировать сценарий PowerShell.
1. Щелкните правой кнопкой в консоли оболочки и выберите **Вставить** .
Создание кластера Azure обозреватель данных и базы данных занимает несколько минут.

#### <a name="verify-the-deployment-using-powershell"></a>Проверка развертывания с помощью PowerShell

Чтобы проверить развертывание, используйте следующий скрипт Azure PowerShell.  Если вы не закрывали Cloud Shell, вам не нужно копировать или выполнять первую строку (для чтения и размещения). Дополнительные сведения об управлении ресурсами обозреватель данных Azure в PowerShell см. в статье [AZ. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Дальнейшие действия

[Прием данных в кластер и базу данных Azure обозреватель данных](ingest-data-overview.md)
