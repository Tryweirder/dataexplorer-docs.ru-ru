---
title: Визуализация данных Azure Data Explorer
description: Узнайте о различных способах визуализации данных Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 6d3b692d72b673b55e4bdc0f737b74b9c4a669d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500792"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Визуализация данных с помощью Azure Data Explorer 

Azure Data Explorer — это быстрая и высокомасштабируемая служба по разведке данных для данных журналов и телеметрии, которая используется для создания сложных аналитических решений для огромных объемов данных. Azure Data Explorer интегрируется с различными инструментами визуализации, так что вы можете визуализировать свои данные и делиться результатами по всей организации. Эти данные могут быть преобразованы в действенные идеи, чтобы оказать влияние на ваш бизнес.

Визуализация данных и отчетность является важным шагом в процессе анализа данных. Azure Data Explorer поддерживает множество служб BI, чтобы можно было использовать тот, который наилучшим образом соответствует сценарию и бюджету.

## <a name="kusto-query-language-visualizations"></a>Визуализации языка запроса Kusto

Язык [`render operator`](kusto/query/renderoperator.md) запроса Kusto предлагает различные визуализации, такие как таблицы, круговые диаграммы и диаграммы баров для отображения результатов запросов. Визуализации запросов полезны при обнаружении и прогнозировании аномалий, машинном обучении и многом другом.

## <a name="power-bi"></a>Power BI

Azure Data Explorer предоставляет возможность подключения к [Power BI](https://powerbi.microsoft.com) с помощью различных методов: 

  * [Встроенный родной разъем Power BI](/azure/data-explorer/power-bi-connector)

  * [Импорт запросов из Azure Data Explorer в Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-запрос](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer предоставляет возможность подключения к [Microsoft Excel](https://products.office.com/excel) с помощью [встроенного разъема Excel](excel-connector.md)или [импортировать запрос](excel-blank-query.md) из Azure Data Explorer в Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) предоставляет плагин Azure Data Explorer, который позволяет визуализировать данные из Azure Data Explorer. Вы [настраиваете Azure Data Explorer в качестве источника данных для Grafana, а затем визуализируете данные.](/azure/data-explorer/grafana) 

## <a name="kibana"></a>Kibana

Azure Data Explorer предоставляет возможность подключения к [Kibana (страница Discover)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) с помощью K2Bridge, разъема с открытым исходным кодом. Вы [настраиваете Azure Data Explorer в качестве источника данных для Kibana, а затем визуализируете данные.](/azure/data-explorer/k2bridge)

## <a name="odbc-connector"></a>Соединитель ODBC

Azure Data Explorer предоставляет [разъем open Database Connectivity (ODBC),](connect-odbc.md) чтобы любое приложение, поддерживающее ODBC, может подключиться к Azure Data Explorer.

## <a name="tableau"></a>Tableau

Azure Data Explorer предоставляет возможность подключения к [Tableau](https://www.tableau.com) с помощью [разъема ODBC,](/azure/data-explorer/connect-odbc) а затем [визуализировать данные в Tableau.](tableau.md)

## <a name="qlik"></a>Qlik

Azure Data Explorer предоставляет возможность подключения к [злику](https://www.qlik.com) с помощью [разъема ODBC,](/azure/data-explorer/connect-odbc) а затем создавать панели мониторинга slik Sense и визуализировать данные. Используя следующее видео, вы можете научиться визуализировать данные Azure Data Explorer с помощью злик. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer предоставляет возможность подключения к [Sisense](https://www.sisense.com) с помощью разъема JDBC. Вы [настраиваете Azure Data Explorer в качестве источника данных для Sisense, а затем визуализируете данные.](/azure/data-explorer/sisense)

## <a name="redash"></a>Redash

Можно использовать [Redash](https://redash.io/) для создания панелей мониторинга и визуализации данных. [Навлаживать Azure Data Explorer в качестве источника данных для Redash, а затем визуализировать данные.](/azure/data-explorer/redash)