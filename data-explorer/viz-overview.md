---
title: Визуализация данных обозреватель данных Azure
description: Узнайте о различных способах визуализации данных Azure обозреватель данных
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: b1d888471c93409826abe523ae6ae4df39e120c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374265"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Визуализация данных с помощью Azure обозреватель данных 

Azure обозреватель данных — это быстрая и Высокомасштабируемая служба просмотра данных для данных журналов и телеметрии, которая используется для создания сложных аналитических решений для больших объемов данных. Azure обозреватель данных интегрируется с различными средствами визуализации, позволяя визуализировать данные и совместно использовать результаты в Организации. Эти данные можно преобразовать в ценную информацию, чтобы повлиять на бизнес.

Визуализация данных и отчетность — это важный шаг в процессе анализа данных. Azure обозреватель данных поддерживает множество служб бизнес-аналитики, что позволяет использовать ту, которая лучше подходит для вашего сценария и бюджета.

## <a name="kusto-query-language-visualizations"></a>Визуализации языка запросов Kusto

Язык запросов Kusto [`render operator`](kusto/query/renderoperator.md) предлагает различные визуализации, такие как таблицы, круговые диаграммы и линейчатые диаграммы для отображения результатов запроса. Визуализации запросов полезны при обнаружении аномалий и прогнозировании, машинном обучении и т. д.

## <a name="power-bi"></a>Power BI

Azure обозреватель данных предоставляет возможность подключения к [Power BI](https://powerbi.microsoft.com) с помощью различных методов: 

  * [Встроенный соединитель Native Power BI](power-bi-connector.md)

  * [Импорт запросов из обозреватель данных Azure в Power BI](power-bi-imported-query.md)
 
  * [SQL-запрос](power-bi-sql-query.md)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure обозреватель данных предоставляет возможность подключения к [Microsoft Excel](https://products.office.com/excel) с помощью [встроенного собственного соединителя Excel](excel-connector.md)или [импорта запроса](excel-blank-query.md) из обозреватель данных Azure в Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) предоставляет подключаемый модуль Azure обозреватель данных, который позволяет визуализировать данные из обозреватель данных Azure. Вы [настраиваете обозреватель данных Azure в качестве источника данных для Grafana, а затем визуализируйте данные](grafana.md). 

## <a name="kibana"></a>Kibana

Azure обозреватель данных предоставляет возможность подключения к [Kibana (странице обнаружения)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) с помощью K2Bridge, соединителя с открытым исходным кодом. Вы [настраиваете обозреватель данных Azure в качестве источника данных для Kibana, а затем визуализируйте данные](k2bridge.md).

## <a name="odbc-connector"></a>Соединитель ODBC

Azure обозреватель данных предоставляет [Соединитель ODBC](connect-odbc.md) , так что любое приложение, которое поддерживает ODBC, может подключаться к Azure обозреватель данных.

## <a name="tableau"></a>Tableau

Azure обозреватель данных предоставляет возможность подключения к [Tableau](https://www.tableau.com) с помощью [соединителя ODBC](connect-odbc.md) и [визуализации данных в Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure обозреватель данных предоставляет возможность подключения к [Qlik Sense](https://www.qlik.com) с помощью [соединителя ODBC](connect-odbc.md) , а затем создает панели мониторинга Qlik senseного датчика и визуализируют данные. С помощью следующего видео вы узнаете, как визуализировать данные обозреватель данных Azure с помощью Qlik Sense. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure обозреватель данных предоставляет возможность подключения к [Sisense](https://www.sisense.com) с помощью соединителя JDBC. Вы [настраиваете обозреватель данных Azure в качестве источника данных для Sisense, а затем визуализируйте данные](sisense.md).

## <a name="redash"></a>Redash

Вы можете использовать [перетире](https://redash.io/) для создания панелей мониторинга и визуализации данных. [Настройте обозреватель данных Azure в качестве источника данных для перештриха, а затем визуализируйте данные](redash.md).
