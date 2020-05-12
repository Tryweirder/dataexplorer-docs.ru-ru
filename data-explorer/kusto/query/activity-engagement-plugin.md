---
title: подключаемый модуль activity_engagement — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль activity_engagement в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9aa85bcb12cd5f8d836f58ea9d16a318d8a40506
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225961"
---
# <a name="activity_engagement-plugin"></a>Подключаемый модуль activity_engagement

вычисляет степень вовлеченности действия на основе идентификатора столбца по скользящей временной шкале.

подключаемый модуль activity_engagement можно использовать для вычисления действий DAU/WAU/MAU (ежедневные, еженедельные и ежемесячные действия).

```kusto
T | evaluate activity_engagement(id, datetime_column, 1d, 30d)
```

**Синтаксис**

*T* `| evaluate` `activity_engagement(` *идколумн* `,` *тимелинеколумн* `,` [*начальный* `,` *конец* `,` ] *иннерактивитивиндов* `,` *аутерактивитивиндов* [ `,` *Dim1* `,` *dim2* `,` ...]`)`

**Аргументы**

* *T*: Входное табличное выражение.
* *Идколумн*: имя столбца со значениями идентификаторов, представляющими действия пользователя. 
* *Тимелинеколумн*: имя столбца, представляющего временную шкалу.
* *Start*(необязательно) — скаляр со значением начального периода анализа.
* *End*(необязательный) — скаляр со значением периода окончания анализа.
* *Иннерактивитивиндов*: Scalar со значением периода окна анализа внутренней области.
* *Аутерактивитивиндов*: скалярный со значением периода окна анализа внешней области.
* *Dim1*, *dim2*,...: (необязательно) список столбцов измерений, которые срезируют вычисление метрик действия.

**Возвращает**

Возвращает таблицу, имеющую (число различных значений ИДЕНТИФИКАТОРов в окне внутренней области, различные значения ИДЕНТИФИКАТОРов в окне внешней области и коэффициент действия) для каждого периода окна внутренней области и для каждого сочетания существующих измерений.

Схема выходной таблицы:

|тимелинеколумн|dcount_activities_inner|dcount_activities_outer|activity_ratio|Dim1|..|dim_n|
|---|---|---|---|--|--|--|--|--|--|
|Тип: от *тимелинеколумн*|long|long|double|..|..|..|


**Примеры**

### <a name="dauwau-calculation"></a>Вычисление DAU/WAU

В следующем примере вычисляется DAU/WAU (коэффициент ежедневных активных пользователей и еженедельных активных пользователей) на основе случайных данных, созданных случайным образом.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-wau.png" border="false" alt-text="Участие в действии Dau WAU":::

### <a name="daumau-calculation"></a>Вычисление DAU/MAU

В следующем примере вычисляется DAU/WAU (коэффициент ежедневных активных пользователей и еженедельных активных пользователей) на основе случайных данных, созданных случайным образом.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau.png" border="false" alt-text="Участие в Dau MAU":::

### <a name="daumau-calculation-with-additional-dimensions"></a>Вычисление DAU/MAU с дополнительными измерениями

В следующем примере вычисляется значение DAU/WAU (для ежедневных активных пользователей и еженедельных активных пользователей) на основе случайно сформированных данных с дополнительным измерением ( `mod3` ).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

:::image type="content" source="images/activity-engagement-plugin/activity-engagement-dau-mau-mod3.png" border="false" alt-text="Участие в действии Dau MAU Mod 3":::
