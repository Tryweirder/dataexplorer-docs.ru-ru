---
title: Преобразование запросов SQL в Kusto в Azure Data Explorer
description: В этой статье описано преобразование запросов SQL в Kusto в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.localizationpriority: high
ms.openlocfilehash: 02326317720cbcc209a21d7687e9597a9a586775
ms.sourcegitcommit: 7a94cdf8c07b15e594dd8abc10091afe5f716609
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543079"
---
# <a name="sql-to-kusto-cheat-sheet"></a>Памятка по преобразованию из SQL в Kusto

Kusto поддерживает подмножество языка SQL. Полный список неподдерживаемых функций см. в статье об [известных проблемах с SQL](../api/tds/sqlknownissues.md).

Основной язык взаимодействия с Kusto — KQL (язык запросов Kusto). Чтобы упростить переход и обучение, можно преобразовать запросы SQL в KQL с помощью Kusto. Отправьте запрос SQL в Kusto, добавив в него префикс EXPLAIN.

Пример:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
EXPLAIN 
SELECT COUNT_BIG(*) as C FROM StormEvents 
```

|Запрос|
|---|
|StormEvents<br>\| summarize C=count()<br>\| project C|

## <a name="sql-to-kusto-cheat-sheet"></a>Памятка по преобразованию из SQL в Kusto

В приведенной ниже таблице показаны примеры запросов в SQL и их эквиваленты KQL.

|Категория |Запросы SQL |Запрос Kusto
|---|---|---
Выбор данных из таблицы |<code>SELECT * FROM dependencies</code> | <code>dependencies</code>
--|<code>SELECT name, resultCode FROM dependencies</code> |<code>dependencies &#124; project name, resultCode</code>
--|<code>SELECT TOP 100 * FROM dependencies</code> | <code>dependencies &#124; take 100</code>
Оценка Null |<code>SELECT * FROM dependencies<br>WHERE resultCode IS NOT NULL</code> | <code>dependencies<br>&#124; where isnotnull(resultCode)</code>
Операторы сравнения (дата) |<code>SELECT * FROM dependencies<br>WHERE timestamp > getdate()-1</code>| <code>dependencies<br>&#124; where timestamp > ago(1d)</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp BETWEEN ... AND ...</code> |<code>dependencies<br>&#124; where timestamp > datetime(2016-10-01)<br>&nbsp;&nbsp;and timestamp <= datetime(2016-11-01)</code>
Операторы сравнения (строка)|<code>SELECT * FROM dependencies<br>WHERE type = "Azure blob"</code> |<code>dependencies<br>&#124; where type == "Azure blob"</code>
--|<code>-- substring<br>SELECT * FROM dependencies<br>WHERE type like "%blob%"</code> |<code>// substring<br>dependencies<br>&#124; where type contains "blob"</code>
--|<code>-- wildcard<br>SELECT * FROM dependencies<br>WHERE type like "Azure%"</code> |<code>// wildcard<br>dependencies<br>&#124; where type startswith "Azure"<br>// or<br>dependencies<br>&#124; where type matches regex "^Azure.*"</code>
Сравнение (логические значения) |<code>SELECT * FROM dependencies<br>WHERE !(success)</code> |<code>dependencies<br>&#124; where success == "False"</code>
Distinct |<code>SELECT DISTINCT name, type  FROM dependencies</code> |<code>dependencies<br>&#124; summarize by name, type</code>
Группирование, агрегирование |<code>SELECT name, AVG(duration) FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize avg(duration) by name</code>
Псевдонимы столбцов, расширение |<code>SELECT operationName as Name, AVG(duration) as AvgD FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize AvgD = avg(duration) by Name=operationName</code>
Упорядочение |<code>SELECT name, timestamp FROM dependencies<br>ORDER BY timestamp ASC</code> |<code>dependencies<br>&#124; project name, timestamp<br>&#124; order by timestamp asc nulls last</code>
Основные n значений по мере |<code>SELECT TOP 100 name, COUNT(*) as Count FROM dependencies<br>GROUP BY name<br>ORDER BY Count DESC</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; top 100 by Count desc</code>
Объединение |<code>SELECT * FROM dependencies<br>UNION<br>SELECT * FROM exceptions</code> |<code>union dependencies, exceptions</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp > ...<br>UNION<br>SELECT * FROM exceptions<br>WHERE timestamp > ...</code> |<code>dependencies<br>&#124; where timestamp > ago(1d)<br>&#124; union<br>&nbsp;&nbsp;(exceptions<br>&nbsp;&nbsp;&#124; where timestamp > ago(1d))</code>
Join |<code>SELECT * FROM dependencies <br>LEFT OUTER JOIN exception<br>ON dependencies.operation_Id = exceptions.operation_Id</code> |<code>dependencies<br>&#124; join kind = leftouter<br>&nbsp;&nbsp;(exceptions)<br>on $left.operation_Id == $right.operation_Id</code>
Вложенные запросы |<code>SELECT * FROM dependencies<br>WHERE resultCode == <br>(SELECT TOP 1 resultCode FROM dependencies<br>WHERE resultId = 7<br>ORDER BY timestamp DESC)</code> |<code>dependencies<br>&#124; where resultCode == toscalar(<br>&nbsp;&nbsp;dependencies<br>&nbsp;&nbsp;&#124; where resultId == 7<br>&nbsp;&nbsp;&#124; top 1 by timestamp desc<br>&nbsp;&nbsp;&#124; project resultCode)</code>
Having |<code>SELECT COUNT(\*) FROM dependencies<br>GROUP BY name<br>HAVING COUNT(\*) > 3</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; where Count > 3</code>|
