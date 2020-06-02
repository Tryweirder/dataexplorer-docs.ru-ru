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
ms.openlocfilehash: b6bc95158c1dd161a17572342c6a99bdf9d37235
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257915"
---
# <a name="azure-data-explorer-tools"></a>Средства Azure Data Explorer

## <a name="ad-hoc-query-tools"></a>Средства для специализированных запросов

* Kusto.Explorer
   * [Установка и пользовательский интерфейс Kusto.Explorer](./kusto-explorer.md) — основное классическое средство для создания запросов и управления в Kusto.
   * [Использование Kusto.Explorer](./kusto-explorer-using.md)
   * [Устранение неполадок с Kusto.Explorer](kusto-explorer-troubleshooting.md)
* [Веб-интерфейс](../../web-query-data.md) — интерфейс для запросов в Kusto.

## <a name="visualizations-dashboards-and-reporting-tools"></a>Средства визуализации, мониторинга и отчетности


* [Записные книжки Azure](../../azure-notebooks.md) — анализируйте данные в Azure Data Explorer с помощью Записных книжек Azure.
* Excel
    * [Пустой запрос Excel](../../excel-blank-query.md) — запрос к Kusto можно добавить в качестве источника данных в Excel.
    * [Соединитель Excel](../../excel-connector.md) — соединитель для подключения Excel к Azure Data Explorer. 

* PowerBI

   * [Power BI. Рекомендации](../../power-bi-best-practices.md)
   * [Power BI. Соединитель](../../power-bi-connector.md)
   * [Power BI. Импортированный запрос](../../power-bi-imported-query.md) 
   * [Power BI. SQL-запрос](../../power-bi-sql-query.md)

* [Grafana](../../grafana.md)
* [Соединитель K2Bridge с открытым кодом](../../k2bridge.md) для визуализации данных из Azure Data Explorer в Kibana.

## <a name="orchestration-tools"></a>Средства оркестрации


* Microsoft Flow
    * [Соединитель Microsoft Flow](../../flow.md)
    * [Примеры использования соединителя Microsoft Flow](../../flow-usage.md)
* [Приложение логики Microsoft](./logicapps.md) — автоматическое выполнение запросов Kusto в составе [приложения логики Microsoft](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



## <a name="data-ingestion-tools"></a>Инструменты приема данных


* [LightIngest](../../lightingest.md) — служебная программа для специализированных операций приема данных в Azure Data Explorer.
* [Функция приема данных одним щелчком](../../ingest-data-one-click.md) — средство, позволяющее быстро принимать данные и автоматически предлагать таблицы и структуры сопоставления.
* [Фабрика данных Azure](azure-data-factory.md).


## <a name="source-control-integration-tools"></a>Средства интеграции систем управления версиями

* [Azure Pipelines](../../devops.md) — вызывает управляющие команды в конвейерах.
* [Синхронизация Kusto](./synckusto.md) — синхронизация хранимых функций из Kusto в Git и/или обратно.
