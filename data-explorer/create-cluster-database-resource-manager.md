---
title: Создание кластера и базы данных Azure Data Explorer с помощью шаблона управления ресурсами Azure
description: Узнайте, как создать кластер и базу данных Azure Data Explorer с помощью шаблона управления ресурсами Azure
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 7a12f43c5c6685ea474f6f19f6e585b09b194fce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496502"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Создание кластера и базы данных Azure Data Explorer с помощью шаблона управления ресурсами Azure

> [!div class="op_single_selector"]
> * [Портал](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Шаблон Azure Resource Manager](create-cluster-database-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Чтобы использовать обозреватель данных Azure, сначала нужно создать кластер и одну или несколько баз данных в этом кластере. Затем вы должны принять (загрузить) данные в базы данных, чтобы к ним можно было выполнять запросы. 

В этой статье вы создаете кластер и базу данных Azure Data Explorer с помощью [шаблона Azure Resource Manager.](/azure/azure-resource-manager/management/overview) Здесь показано, как определить развертываемые ресурсы и параметры, указываемые при развертывании. Этот шаблон можно использовать для собственных развертываний или настроить его в соответствии с вашими требованиями. Для получения [информации](/azure/azure-resource-manager/resource-group-authoring-templates)о создании шаблонов см. Для использования в шаблоне синтаксиса и свойств JSON [см.](/azure/templates/microsoft.kusto/allversions)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Шаблон управления ресурсами Azure для создания кластеров и баз данных

В этой статье используется [существующий шаблон быстрого запуска](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

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

Шаблон управления ресурсами Azure можно развернуть [с помощью портала Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) или [с помощью powershell.](#use-powershell-to-deploy-the-template-and-verify-template-deployment)

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Используйте портал Azure для развертывания шаблона и проверки развертывания шаблонов

1. Чтобы создать кластер и базу данных, используйте следующую кнопку для начала развертывания. Чтобы закончить выполнение инструкций из этой статьи, щелкните правой кнопкой мыши и выберите ссылку **Открыть в новом окне**.

    [![Развертывание в Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Кнопка **Развернуть в Azure** выполняет переход на портал Azure для заполнения формы развертывания.

    ![Развертывание в Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Шаблон можно [отсеивать и развертывать на портале Azure,](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) используя форму.

1. Заполните **разделы BASICS** и **SETTINGS.** Выберите уникальные названия кластеров и баз данных.
Создание кластера и базы данных Azure Data Explorer занимает несколько минут.

1. Чтобы проверить развертывание, вы открываете группу ресурсов [на портале Azure](https://portal.azure.com) для поиска нового кластера и базы данных. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Используйте powershell для развертывания шаблона и проверки развертывания шаблонов

#### <a name="deploy-the-template-using-powershell"></a>Развертывание шаблона с помощью powershell

1. Выберите **Попробовать** в следующем блоке кода и следуйте инструкциям, чтобы войти в Azure Cloud Shell.

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

1. Нажмите кнопку **Копировать**, чтобы скопировать сценарий PowerShell.
1. Щелкните правой кнопкой в консоли оболочки и выберите **Вставить**.
Создание кластера и базы данных Azure Data Explorer занимает несколько минут.

#### <a name="verify-the-deployment-using-powershell"></a>Проверить развертывание с помощью PowerShell

Для проверки развертывания используйте следующий скрипт Azure PowerShell.  Если облачная оболочка по-прежнему открыта, вам не нужно копировать/запускать первую строку (Read-Host). Для получения дополнительной информации об управлении ресурсами Azure Data Explorer в PowerShell, читайте [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Следующие шаги

[Ввоз данных в кластер и базу данных Azure Data Explorer](ingest-data-overview.md)
