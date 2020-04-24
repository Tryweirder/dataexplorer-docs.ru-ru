---
title: sliding_window_counts плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается sliding_window_counts плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: feab3d0e8f548817be12f202eb2d494bd65aa133
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507503"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts плагин

Вычисляет подсчеты и отчетливое количество значений в раздвижном окне в течение периода возврата, используя технику, описанную [здесь.](samples.md#performing-aggregations-over-a-sliding-window)

Например, за каждый *день,* рассчитать количество и различные подсчета пользователей на предыдущей *неделе.* 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**Синтаксис**

*T* `| evaluate` *dim1* `,` *dim2* `,` *End* `,` `,` *Bin* `,` *Start* `,` `,` *IdColumn* `,` *TimelineColumn* *LookbackWindow* IdColumn TimelineКолонка Начало Конец LookbackWindow Бин `sliding_window_counts(``)`

**Аргументы**

* *T*: Входное табеляцивское выражение.
* *IdColumn*: Название столбца с значениями идентификатора, которые представляют активность пользователя. 
* *TimelineColumn*: Название столбца, представляющего временную шкалу.
* *Начало*: Scalar со значением периода начала анализа.
* *Конец*: Scalar со значением конечного периода анализа.
* *LookbackWindow*: Scalar постоянное значение периода возврата (например, для пользователей, считаюющихся в прошлом 7d: LookbackWindow 7d)
* *Бин*: Scalar константное значение периода шага анализа. Может быть либо числовая / дата / timestamp значение, `week` / `month` / `year`или строка, которая является одним из , и в этом случае все периоды будут [startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md) соответственно. 
* *dim1*, *dim2*, ...: (необязательный) список столбцов измерений, которые нарезает расчет метрик активности.

**Возвращает**

Возвращает таблицу с значениями подсчета и различных значений подсчета идентификаторов в период обратного отсчета, для каждого периода временной шкалы (по ячейке) и для каждой существующей комбинации измерений.

Схема таблицы вывода:

|*ХронологияКоля*|dim1|..|dim_n|Count|Численность счета|
|---|---|---|---|---|---|
|тип: по состоянию на *ХроникаColumn*|..|..|..|long|long|


**Примеры**

Рассчитать подсчеты и количество счетов для пользователей за прошедшую неделю, за каждый день в период анализа. 

```kusto
let start = datetime(2017 - 08 - 01);
let end = datetime(2017 - 08 - 07); 
let lookbackWindow = 3d;  
let bin = 1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'Bob',      datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'John',     datetime(2017 - 08 - 01), 
'Bob',      datetime(2017 - 08 - 01), 
'Ananda',   datetime(2017 - 08 - 02),  
'Atul',     datetime(2017 - 08 - 02), 
'John',     datetime(2017 - 08 - 02), 
'Ananda',   datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'John',     datetime(2017 - 08 - 03), 
'Bob',      datetime(2017 - 08 - 03), 
'Betsy',    datetime(2017 - 08 - 04), 
'Bob',      datetime(2017 - 08 - 05), 
];
T | evaluate sliding_window_counts(UserId, Timestamp, start, end, lookbackWindow, bin)


```

|Отметка времени|Count|Численность счета|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|