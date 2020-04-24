---
title: dcount_intersect плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается dcount_intersect плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7771456ffa75085c79933c2e789e3d98f352b76f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516139"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersect плагин

Вычисляет пересечение между наборами N на основе значений hll (N в диапазоне 2...16) и возвращает значения N ccount.

Учитывая наборы S<sub>1,</sub>S<sub>2</sub>, .. S<sub>n</sub> - значения возврата будут представлять различные показатели:  
S<sub>1</sub>, S<sub>1</sub> 'S<sub>2</sub>,  
S<sub>1</sub> "S<sub>2"</sub> S<sub>3</sub>,  
... ,  
S<sub>1</sub> "S<sub>2</sub> " ... S<sub>n</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**Синтаксис**

*T* `| evaluate` T `dcount_intersect(` *hll_1,* *hll_2,*`,` *hll_3* `,` ...»`)`

**Аргументы**

* *T*: Входное табеляцивское выражение.
* *hll_i*: значения набора S<sub>i</sub> вычислялись с функцией [hll()](./hll-aggfunction.md) .

**Возвращает**

Возвращает таблицу со значениями N dcount (на столбцы столбцов, представляющих наборы пересечений).
Названия колонн s0, s1, ... (до n-1).

**Примеры**

```kusto
// Generate numbers from 1 to 100
range x from 1 to 100 step 1
| extend isEven = (x % 2 == 0), isMod3 = (x % 3 == 0), isMod5 = (x % 5 == 0)
// Calculate conditional HLL values (note that '0' is included in each of them as additional value, so we will subtract it later)
| summarize hll_even = hll(iif(isEven, x, 0), 2),
            hll_mod3 = hll(iif(isMod3, x, 0), 2),
            hll_mod5 = hll(iif(isMod5, x, 0), 2) 
// Invoke the plugin that calculates dcount intersections         
| evaluate dcount_intersect(hll_even, hll_mod3, hll_mod5)
| project evenNumbers = s0 - 1,             //                             100 / 2 = 50
          even_and_mod3 = s1 - 1,           // gcd(2,3) = 6, therefor:     100 / 6 = 16
          even_and_mod3_and_mod5 = s2 - 1   // gcd(2,3,5) is 30, therefore: 100 / 30 = 3 
```

|evenNumbers|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|