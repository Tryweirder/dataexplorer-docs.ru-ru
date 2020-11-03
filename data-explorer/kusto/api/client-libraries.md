---
title: Общие сведения о клиентских библиотеках Azure обозреватель данных
description: В этой статье перечислены клиентские библиотеки в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f99b37d3de5acb30cd427d808138686f35a96305
ms.sourcegitcommit: 46ae22f0f1eda2a21ac01de8d0c2fa2f57de0ad3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93238290"
---
# <a name="client-libraries"></a>Клиентские библиотеки

В следующей таблице перечислены различные библиотеки, предоставляемые для запросов, приема и управления ARM/RP. Используйте эти библиотеки для интерфейсов API Azure и программного вызова функций обозреватель данных Azure. 


|    Язык и функциональность        |    Запрос        |    Прием        |    Управление ARM/RP        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .NET        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)         |
|    .NET Standard        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2020_09_18)        |
|    JavaScript        |             |             |    [npm](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    Node.js        |    [NPM](https://www.npmjs.com/package/azure-kusto-data) [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [NPM](https://www.npmjs.com/package/azure-kusto-ingest)       [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)        |    [npm](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [PyPI](https://pypi.org/project/azure-kusto-ingest/)    [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [PyPI](https://pypi.org/project/azure-kusto-data/)      [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [PyPI](https://pypi.org/project/azure-mgmt-kusto/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt)        |
|    Ruby        |             |             |    [Драгоцен]( https://rubygems.org/gems/azure_mgmt_kusto)         |
|    PowerShell        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [Пакет](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)         |
|    REST API        |    [REST](rest/index.md)        |    [REST](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [NPM](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>Средства и интеграции

* Самое освещение: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* Набор приема одним щелчком: [GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* Kafka: [GitHub](https://github.com/Azure/kafka-sink-azure-kusto)
* Logstash: [GitHub](https://github.com/Azure/logstash-output-kusto) 
* Spark: [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
