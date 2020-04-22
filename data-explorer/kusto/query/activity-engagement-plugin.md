---
title: activity_engagement плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается activity_engagement плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fe6d3f68f59ae52c96d3e9467cc364d792b13cf3
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664034"
---
# <a name="activity_engagement-plugin"></a>Подключаемый модуль activity_engagement

вычисляет степень вовлеченности действия на основе идентификатора столбца по скользящей временной шкале.

activity_engagement плагин может быть использован для расчета DAU/WAU/MAU (ежедневная/еженедельная/ежемесячная деятельность).

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**Синтаксис**

*T* `| evaluate` `,` *End*`,``,` `,` `,` `,` *IdColumn* `,` `,` *dim2* *dim1* *Start* *InnerActivityWindow* *OuterActivityWindow* *TimelineColumn* IdColumn СрокиКолонка - Стартовый конец - InnerActivityWindow OuterActivityWindow - dim1 dim2 ..." `activity_engagement(``)`

**Аргументы**

* *T*: Входное табеляцивское выражение.
* *IdColumn*: Название столбца с значениями идентификатора, которые представляют активность пользователя. 
* *TimelineColumn*: Название столбца, представляющего временную шкалу.
* *Начало*: (необязательно) Scalar со значением периода начала анализа.
* *Конец*: (необязательно) Scalar со значением конечного периода анализа.
* *InnerActivityWindow*: Scalar со значением периода внутреннего анализа.
* *OuterActivityWindow*: Scalar со значением периода окна внешнего анализа.
* *dim1*, *dim2*, ...: (необязательный) список столбцов измерений, которые нарезает расчет метрик активности.

**Возвращает**

Возвращает таблицу, которая имеет (четкое количество значений идентификатора внутри окна внутренней области, четкое количество значений идентификатора внутри окна внешней области и соотношение активности) для каждого периода окна внутреннего объема и для каждой существующей комбинации измерений.

Схема таблицы вывода:

|ХронологияКоля|dcount_activities_inner|dcount_activities_outer|activity_ratio|dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|тип: по состоянию на *ХроникаColumn*|long|long|double|..|..|..|


**Примеры**

### <a name="dauwau-calculation"></a>Расчет DAU/WAU

Следующий пример вычисляет DAU/WAU (ежедневное отношение активных пользователей / Еженедельных активных пользователей) по случайным данным.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/queries/activity-engagement-dau-wau.png" border="false" alt-text="Активность участия дау Вау":::

### <a name="daumau-calculation"></a>Расчет DAU/MAU

Следующий пример вычисляет DAU/WAU (ежедневное отношение активных пользователей / Еженедельных активных пользователей) по случайным данным.

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d)
| project _day, Dau_Mau=activity_ratio*100 
| render timechart 
```

:::image type="content" source="images/queries/activity-engagement-dau-mau.png" border="false" alt-text="Активность участия дау Мау Мау":::

### <a name="daumau-calculation-with-additional-dimensions"></a>Расчет DAU/MAU с дополнительными размерами

Следующий пример вычисляет DAU/WAU (ежедневное отношение активных пользователей /`mod3`Еженедельные активные пользователи) по случайно сгенерированным данным с дополнительным измерением ().

```kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-05-31);
range _day from _start to _end  step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
| extend mod3 = strcat("mod3=", id % 3)
// Calculate DAU/MAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 30d, mod3)
| project _day, Dau_Mau=activity_ratio*100, mod3 
| render timechart 
```

:::image type="content" source="images/queries/activity-engagement-dau-mau-mod3.png" border="false" alt-text="Активность участия дау Мау мод 3":::
