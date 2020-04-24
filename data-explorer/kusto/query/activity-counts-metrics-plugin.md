---
title: activity_counts_metrics плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается activity_counts_metrics плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e55cd940850440499d227082f57769e499e6a551
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519318"
---
# <a name="activity_counts_metrics-plugin"></a>Подключаемый модуль activity_counts_metrics

Высчитывает полезные показатели активности (общие значения подсчета, различные значения количества, различные значения новых значений, агрегированный отчетный счет) для каждого временного окна, сравниваемого/агрегированного в/с *activity_metrics* плагином (в отличие от [activity_metrics плагина,](activity-metrics-plugin.md) в котором каждое временное окно сравнивается только с его предыдущим временным окном).

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**Синтаксис**

*T* `| evaluate` *End* `,` *Window* `,` `,` `,` `,` *IdColumn* `,` `,` `,` *Start* *dim2* *Cohort* *dim1* *TimelineColumn* IdColumn TimelineКолонка Стартовый конец окна » Когорта »Dim1 dim2 ...» `activity_counts_metrics(` `,` *Оглядывай назад*`)`

**Аргументы**

* *T*: Входное табеляцивское выражение.
* *IdColumn*: Название столбца с значениями идентификатора, которые представляют активность пользователя. 
* *TimelineColumn*: Название столбца, представляющего временную шкалу.
* *Начало*: Scalar со значением периода начала анализа.
* *Конец*: Scalar со значением конечного периода анализа.
* *Окно*: Scalar со значением периода окна анализа. Может быть либо числовая / дата / timestamp значение, `week` / `month` / `year`или строка, которая является одним из , и в этом случае все периоды будут [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) соответственно. 
* *dim1*, *dim2*, ...: (необязательный) список столбцов измерений, которые нарезает расчет метрик активности.

**Возвращает**

Возвращает таблицу с общими значениями подсчета, различными значениями количества, определенным подсчетом новых значений, агрегированным определенным подсчетом для каждого временного окна.

Схема таблицы вывода:

|ХронологияКоля|dim1|...|dim_n|count|dcount|new_dcount|aggregated_dcount
|---|---|---|---|---|---|---|---|---|
|тип: по состоянию на *ХроникаColumn*|..|..|..|long|long|long|long|long


* *ХронологияКолонка*: Время начала временного окна.
* *отсчет*: Общее количество записей в окне времени и *тусклом (ы)*
* *dcount*: Различные значения идентификатора подсчитывают в окне времени и *тусклый(ы)*
* *new_dcount*: Различные значения идентификатора в временном окне и *тусклый (ы)* по сравнению со всеми предыдущими окнами времени. 
* *aggregated_dcount*: Общие агрегированные различные значения идентификатора *dim (ы)* от 1-го временного окна до тока (включительно).

**Примеры**

### <a name="daily-activity-counts"></a>Количество ежедневных действий 

Следующий запрос вычисляет ежедневные значения активности для предоставленной таблицы ввода

```kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01), 
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),  
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
 T 
 | evaluate activity_counts_metrics(UserId, Timestamp, start, end, window)
```

|Отметка времени|count|dcount|new_dcount|aggregated_dcount|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


