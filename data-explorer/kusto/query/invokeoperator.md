---
title: вызов оператора с обозреватель данных Azure
description: В этой статье описывается оператор Invoke в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1aca8cda34e1ee8506d5be6633cfd46fd912c6c3
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271525"
---
# <a name="invoke-operator"></a>Оператор invoke

Вызывает лямбда-выражение, получающее источник в `invoke` качестве аргумента табличного параметра.

```kusto
T | invoke foo(param1, param2)
```

**Синтаксис**

`T | invoke`*функция* `(` [*param1* `,` *Param2*]`)`

**Аргументы**

* *T*: табличный источник.
* *Function*: имя лямбда-выражения или имени функции для оценки.
* *param1*, *Param2* ...: дополнительные лямбда-аргументы.

**Возвращает**

Возвращает результат вычисленного выражения.

**Примечания**

Дополнительные сведения об объявлении лямбда-выражений, которые могут принимать табличные аргументы, см. в разделе [инструкции Let](./letstatement.md) .

**Пример**

В следующем примере показано, как использовать `invoke` оператор для вызова лямбда-выражения:

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
