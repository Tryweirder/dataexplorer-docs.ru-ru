---
title: Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью шаблона Azure Resource Manager
description: Из этой статьи вы узнаете, как создать подключение к данным концентратора событий для Azure обозреватель данных с помощью шаблона Azure Resource Manager.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: 5764f4c2adeb40e3b08a013489d2e58a96b52c6b
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714097"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-azure-resource-manager-template"></a>Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью шаблона Azure Resource Manager

> [!div class="op_single_selector"]
> * [Портал](ingest-data-event-hub.md)
> * [Один щелчок](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Шаблон Azure Resource Manager](data-connection-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)] 
В этой статье вы создадите подключение к данным концентратора событий для Azure обозреватель данных с помощью шаблона Azure Resource Manager.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.
* Создание [кластера и базы данных](create-cluster-database-portal.md)
* Создание [сопоставления таблицы и столбца](ingest-data-event-hub.md#create-a-target-table-in-azure-data-explorer)
* Создание [концентратора событий](/azure/event-hubs/event-hubs-create)

## <a name="azure-resource-manager-template-for-adding-an-event-hub-data-connection"></a>Шаблон Azure Resource Manager для добавления подключения к данным концентратора событий

В следующем примере показан шаблон Azure Resource Manager для добавления подключения к данным концентратора событий.  Шаблон можно [изменить и развернуть в портал Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) с помощью формы.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namespaces_eventhubns_name": {
            "type": "string",
            "defaultValue": "eventhubns",
            "metadata": {
                "description": "Specifies the Event Hub Namespace name."
            }
        },
        "EventHubs_eventhubdemo_name": {
            "type": "string",
            "defaultValue": "eventhubdemo",
            "metadata": {
                "description": "Specifies the Event Hub name."
            }
        },
        "consumergroup_default_name": {
            "type": "string",
            "defaultValue": "$Default",
            "metadata": {
                "description": "Specifies the consumer group of the Event Hub."
            }
        },
        "Clusters_kustocluster_name": {
            "type": "string",
            "defaultValue": "kustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databases_kustodb_name": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "tables_kustotable_name": {
            "type": "string",
            "defaultValue": "kustotable",
            "metadata": {
                "description": "Specifies the name of the table"
            }
        },
        "mapping_kustomapping_name": {
            "type": "string",
            "defaultValue": "kustomapping",
            "metadata": {
                "description": "Specifies the name of the mapping rule"
            }
        },
        "dataformat_type": {
            "type": "string",
            "defaultValue": "csv",
            "metadata": {
                "description": "Specifies the data format"
            }
        },
        "dataconnections_kustodc_name": {
            "type": "string",
            "defaultValue": "kustodc",
            "metadata": {
                "description": "Name of the data connection to create"
            }
        },
        "subscriptionId": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "Specifies the subscriptionId of the Event Hub"
            }
        },
        "resourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "metadata": {
                "description": "Specifies the resourceGroup of the Event Hub"
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
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/DataConnections",
            "apiVersion": "2019-09-07",
            "name": "[concat(parameters('Clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'), '/', parameters('dataconnections_kustodc_name'))]",
            "location": "[parameters('location')]",
            "kind": "EventHub",
            "properties": {
                "eventHubResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.EventHub/namespaces/eventhubs', parameters('namespaces_eventhubns_name'), parameters('EventHubs_eventhubdemo_name'))]",
                "consumerGroup": "[parameters('consumergroup_default_name')]",
                "tableName": "[parameters('tables_kustotable_name')]",
                "mappingRuleName": "[parameters('mapping_kustomapping_name')]",
                "dataFormat": "[parameters('dataformat_type')]"
            }
        }
    ]
}
```

[!INCLUDE [data-explorer-clean-resources](includes/data-explorer-clean-resources.md)]