---
title: Сведения о средствах Azure Data Explorer (Azure Data Explorer) | Документация Майкрософт
description: В этой статье представлены сведения о средствах Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: e9b274ae129f7cd15ba30edb24f8432c738f55ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490656"
---
# <a name="azure-data-explorer-tools"></a>Средства Azure Data Explorer

## <a name="ad-hoc-query-tools"></a>Средства для специализированных запросов


* [Kusto.Explorer](./kusto-explorer.md) — основное средство для запросов и управления в Kusto.
* [Веб-интерфейс](https://docs.microsoft.com/azure/data-explorer/web-query-data) — интерфейс для запросов в Kusto.

## <a name="visualizations-dashboards-and-reporting-tools"></a>Средства визуализации, мониторинга и отчетности


* [Записные книжки Azure](https://docs.microsoft.com/azure/data-explorer/azure-notebooks) — анализируйте данные в Azure Data Explorer с помощью Записных книжек Azure.
* Excel
    * [Пустой запрос Excel](https://docs.microsoft.com/azure/data-explorer/excel-blank-query) — запрос к Kusto можно добавить в качестве источника данных в Excel.
    * [Соединитель Excel](https://docs.microsoft.com/azure/data-explorer/excel-connector) — соединитель для подключения Excel к Azure Data Explorer. 

* PowerBI

   * [Power BI. Рекомендации](https://docs.microsoft.com/azure/data-explorer/power-bi-best-practices)
   * [Power BI. Соединитель](https://docs.microsoft.com/azure/data-explorer/power-bi-connector)
   * [Power BI. Импортированный запрос](https://docs.microsoft.com/azure/data-explorer/power-bi-imported-query) 
   * [Power BI. SQL-запрос](https://docs.microsoft.com/azure/data-explorer/power-bi-sql-query)

* [Grafana](https://docs.microsoft.com/azure/data-explorer/grafana)

## <a name="orchestration-tools"></a>Средства оркестрации


* Microsoft Flow
    * [Соединитель Microsoft Flow](https://docs.microsoft.com/azure/data-explorer/flow)
    * [Примеры использования соединителя Microsoft Flow](https://docs.microsoft.com/azure/data-explorer/flow-usage)
* [Приложение логики Microsoft](./logicapps.md) — автоматическое выполнение запросов Kusto в составе [приложения логики Microsoft](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



## <a name="data-ingestion-tools"></a>Инструменты приема данных


* [LightIngest](https://docs.microsoft.com/azure/data-explorer/lightingest) — служебная программа для специализированных операций приема данных в Azure Data Explorer.
 



## <a name="source-control-integration-tools"></a>Средства интеграции систем управления версиями

* [Azure Pipelines](https://docs.microsoft.com/azure/data-explorer/devops) — вызывает управляющие команды в конвейерах.
* [Синхронизация Kusto](./synckusto.md) — синхронизация хранимых функций из Kusto в Git и/или обратно.
