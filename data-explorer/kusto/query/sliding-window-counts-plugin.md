---
title: подключаемый модуль sliding_window_counts — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль sliding_window_counts в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9234745a002b88acf23b2e177aa8bc11cda45857
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241796"
---
# <a name="sliding_window_counts-plugin"></a>Подключаемый модуль sliding_window_counts

Вычисляет количество и число различных значений в скользящем окне за лукбакк период с помощью описываемого [здесь](samples.md#perform-aggregations-over-a-sliding-window)метода.

Например, для каждого *дня*можно вычислить количество и число уникальных пользователей за прошлую *неделю*. 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Синтаксис

*T* `| evaluate` `sliding_window_counts(` *идколумн* `,` *тимелинеколумн* `,` *Start* `,` *End* `,` *лукбакквиндов* `,` *bin* `,` [*Dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>Аргументы

* *T*: Входное табличное выражение.
* *Идколумн*: имя столбца со значениями идентификаторов, представляющими действия пользователя. 
* *Тимелинеколумн*: имя столбца, представляющего временную шкалу.
* *Начало*: скалярное значение начального периода анализа.
* *End*: скаляр со значением периода окончания анализа.
* *Лукбакквиндов*: скалярное постоянное значение периода лукбакк (например, для `dcount` пользователей в прошлом 7D: лукбакквиндов = 7D)
* *Bin*: скалярное постоянное значение периода анализа. Это значение может быть числовым значением, DateTime или timestamp. Если значение является строкой с форматом `week` / `month` / `year` , все точки будут [startofweek](startofweekfunction.md) / [StartOfMonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md). 
* *Dim1*, *dim2*,...: (необязательно) список столбцов измерений, которые срезируют вычисление метрик действия.

## <a name="returns"></a>Результаты

Возвращает таблицу, которая содержит значения Count и Distinct Count идентификаторов в лукбакк периоде для каждого периода временной шкалы (по ячейке) и для каждой комбинации существующих измерений.

Схема выходной таблицы:

|*тимелинеколумн*|`dim1`|..|`dim_n`|`count`|`dcount`|
|---|---|---|---|---|---|
|Тип: от *тимелинеколумн*|..|..|..|long|long|


## <a name="examples"></a>Примеры

Вычислите количество и `dcounts` для пользователей за прошлую неделю за каждый день анализа. 

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

|Отметка времени|Счетчик|`dcount`|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|