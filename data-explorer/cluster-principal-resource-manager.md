---
title: Добавление основкластеров для исследователя данных Azure с помощью шаблона управления ресурсами Azure
description: В этой статье вы узнаете, как добавить принципы кластеров для Azure Data Explorer с помощью шаблона Управления ресурсами Azure.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 22423568ab0b3b55d8d9566df4829eb6070b5f8c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497009"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Добавление основкластеров для исследователя данных Azure с помощью шаблона управления ресурсами Azure

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Шаблон Azure Resource Manager](cluster-principal-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы добавляете принципы кластеров для Azure Data Explorer с помощью шаблона Azure Resource Manager.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* [Создание кластера](create-cluster-database-portal.md).

## <a name="azure-resource-manager-template-for-adding-a-cluster-principal"></a>Шаблон менеджера ресурсов Azure для добавления основного кластера

В следующем примере показан шаблон менеджера ресурсов Azure для добавления основного кластера.  Шаблон можно [отсеивать и развертывать на портале Azure,](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) используя форму.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterPrincipalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "principalIdForCluster": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForClusterPrincipal": {
            "type": "string",
            "defaultValue": "AllDatabasesViewer",
            "metadata": {
                "description": "Specifies the cluster principal role. It can be 'AllDatabasesAdmin', 'AllDatabasesViewer'"
            }
        },
        "tenantIdForClusterPrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the principal"
            }
        },
        "principalTypeForCluster": {
            "type": "string",
            "defaultValue": "User",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('clusterPrincipalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForCluster')]",
                "role": "[parameters('roleForClusterPrincipal')]",
                "tenantId": "[parameters('tenantIdForClusterPrincipal')]",
                "principalType": "[parameters('principalTypeForCluster')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Следующие шаги

* [Добавление основ базы данных](database-principal-resource-manager.md)
