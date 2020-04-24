---
title: Общие сведения о клиентских библиотеках Azure обозреватель данных | Документация Майкрософт
description: В этой статье рассматриваются общие сведения о клиентских библиотеках в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 2a8e66e726bd4dbd49cf402178117d9d01c65d34
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108190"
---
# <a name="client-libraries-overview"></a>Общие сведения о клиентских библиотеках

В следующей таблице перечислены различные библиотеки, предоставляемые для запросов, приема и управления ARM/RP. Использование этих библиотек является рекомендуемым способом использования интерфейсов API Azure и программного вызова функций Azure обозреватель данных. 


|    лангуаже\функтионалити        |    Запрос        |    Прием        |    Управление ARM/RP        |
|------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |--------------------------------------------------------------------------------------------------------------------------------------------------------------------    |------------------------------------------------------------------------------------------------------------------------------    |
|    .Net        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)            |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/1.0.0)         |
|    .NET Standard        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard/)        |            |
|    Java        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/data)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest) [GitHub](https://github.com/Azure/azure-kusto-java/tree/master/ingest)        |    [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto.v2019_01_21/azure-mgmt-kusto)        |
|    JavaScript        |             |             |    [NPM](https://www.npmjs.com/package/@azure/arm-kusto)         |
|    Node.js        |    [NPM](https://www.npmjs.com/package/azure-kusto-data) [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)        |    [NPM](https://www.npmjs.com/package/azure-kusto-ingest)       [GitHub](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)        |    [NPM](https://www.npmjs.com/package/azure-arm-kusto/v/2.0.0)        |
|    Python        |    [PyPI](https://pypi.org/project/azure-kusto-ingest/)    [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)        |    [PyPI](https://pypi.org/project/azure-kusto-data/)      [GitHub](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)        |    [PyPI](https://pypi.org/project/azure-mgmt-kusto/0.3.0/)        |
|    R        |    [CRAN](https://cran.r-project.org/web/packages/AzureKusto/index.html)               |             |            |
|    Go        |    [GitHub](https://github.com/Azure/azure-kusto-go)        |    [GitHub](https://github.com/Azure/azure-kusto-go/tree/master/kusto/ingest)        |        [GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/kusto/mgmt/2019-01-21/kusto)        |
|    Ruby        |             |             |    [Драгоцен](https://rubygems.org/gems/azure_mgmt_kusto/versions/0.17.1)         |
|    PowerShell.        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)        |    [Пакет](https://www.powershellgallery.com/packages/Az.Kusto/)         |
|    Azure CLI        |             |             |    [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)         |
|    REST API        |    [REST](rest/index.md)        |    [REST](rest/index.md)        |     [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/azure-kusto/resource-manager/Microsoft.Kusto)         |
|    TypeScript        |             |             |        [NPM](https://www.npmjs.com/package/@azure/arm-kusto/v/2.0.0)        |
|      |      |      |      |


## <a name="tools-and-integrations"></a>Средства и интеграции

* Самое освещение: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/) 
* Набор приема одним щелчком: [GitHub](https://github.com/Azure/azure-kusto-ingestion-tools) 
* Kafka: [GitHub](https://github.com/Azure/kafka-sink-azure-kusto)
* Logstash: [GitHub](https://github.com/Azure/logstash-output-kusto) 
* Spark: [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)