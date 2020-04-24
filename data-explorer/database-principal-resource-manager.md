---
title: Добавление основной базы данных для исследователя данных Azure с помощью шаблона управления ресурсами Azure
description: В этой статье вы узнаете, как добавить принципы базы данных для Azure Data Explorer с помощью шаблона Управления ресурсами Azure.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 80fcf4b6c5d64dde7bd1c5475fb6a3dbafd7f907
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496437"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Добавление основ базы данных для исследователя данных Azure с помощью шаблона управления ресурсами Azure

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Шаблон Azure Resource Manager](database-principal-resource-manager.md)

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. В этой статье вы добавляете принципы базы данных для Azure Data Explorer с помощью шаблона Azure Resource Manager.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера и базы данных](create-cluster-database-portal.md)

## <a name="azure-resource-manager-template-for-adding-a-database-principal"></a>Шаблон менеджера ресурсов Azure для добавления основного принципа базы данных

В следующем примере показан шаблон менеджера ресурсов Azure для добавления основного элемента базы данных.  Шаблон можно [отсеивать и развертывать на портале Azure,](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) используя форму.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalAssignmentName": {
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
        "databaseName": {
            "type": "string",
            "defaultValue": "mykustodatabase",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "principalIdForDatabase": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForDatabasePrincipal": {
            "type": "string",
            "defaultValue": "Admin",
            "metadata": {
                "description": "Specifies the database principal role. It can be 'Admin', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer'"
            }
        },
        "tenantIdForDatabasePrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the database principal"
            }
        },
        "principalTypeForDatabase": {
            "type": "string",
            "defaultValue": "App",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('databaseName'), '/', parameters('principalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForDatabase')]",
                "role": "[parameters('roleForDatabasePrincipal')]",
                "tenantId": "[parameters('tenantIdForDatabasePrincipal')]",
                "principalType": "[parameters('principalTypeForDatabase')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Следующие шаги

* [Прием данных из концентратора событий в Azure Data Explorer](ingest-data-event-hub.md)
