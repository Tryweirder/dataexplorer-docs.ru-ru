---
title: подключаемый модуль activity_counts_metrics — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль activity_counts_metrics в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f44af0b7c1623bfd8393ddeefee57f7f8ca27d2b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241736"
---
# <a name="activity_counts_metrics-plugin"></a>Подключаемый модуль activity_counts_metrics

Вычисляет полезные метрики действий для каждого временного окна, сравниваемого или агрегированного со *всеми* предыдущими окнами времени. Метрики включают в себя: общее количество значений, число различных значений, число различных новых значений и агрегированное число различных объектов. Сравните этот подключаемый модуль с [activity_metrics подключаемым модулем](activity-metrics-plugin.md), в котором каждый интервал времени сравнивается только с предыдущим окном времени.

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>Синтаксис

*T* `| evaluate` `activity_counts_metrics(` *идколумн* `,` *тимелинеколумн* `,` *Start* `,` *End* `,` *Window* [ `,` *когорту*] [ `,` *Dim1* `,` *dim2* `,` ...] [ `,` *лукбакк*]`)`

## <a name="arguments"></a>Аргументы

* *T*: Входное табличное выражение.
* *Идколумн*: имя столбца со значениями идентификаторов, представляющими действия пользователя. 
* *Тимелинеколумн*: имя столбца, представляющего временную шкалу.
* *Начало*: скалярное значение начального периода анализа.
* *End*: скаляр со значением периода окончания анализа.
* *Window*: скалярный со значением периода окна анализа. Аргумент может быть либо числовым значением, либо DateTime/timestamp, либо строкой, которая является одним из `week` / `month` / `year` , в этом случае все периоды будут [startofweek](startofweekfunction.md) / [StartOfMonth](startofmonthfunction.md) или [startofyear](startofyearfunction.md). 
* *Dim1*, *dim2*,...: (необязательно) список столбцов измерений, которые срезируют вычисление метрик действия.

## <a name="returns"></a>Результаты

Возвращает таблицу, имеющую следующие значения: общее количество значений, число различных значений, количество различных новых значений и агрегированное число различных элементов для каждого временного интервала.

Схема выходной таблицы:

|`TimelineColumn`|`dim1`|...|`dim_n`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`
|---|---|---|---|---|---|---|---|---|
|Тип: по отношению к *`TimelineColumn`*|..|..|..|long|long|long|long|long


* *`TimelineColumn`*: Время начала временного окна.
* *`count`*: Общее число записей в окне "время" и *затемнение (s)*
* *`dcount`*: Количество уникальных значений ИДЕНТИФИКАТОРов в временном окне и *Dim (s)*
* *`new_dcount`*: Уникальные значения идентификатора в окне времени и *Dim (s)* по сравнению со всеми предыдущими окнами времени. 
* *`aggregated_dcount`*: Общее число уникальных значений ИДЕНТИФИКАТОРов *(Dim)* , заданных с первого периода до текущего (включительно).

## <a name="examples"></a>Примеры

### <a name="daily-activity-counts"></a>Число ежедневных действий 

Следующий запрос вычисляет ежедневные счетчики активности для указанной входной таблицы.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

|`Timestamp`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


