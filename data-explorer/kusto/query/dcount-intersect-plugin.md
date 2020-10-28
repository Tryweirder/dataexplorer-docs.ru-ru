---
title: подключаемый модуль dcount_intersect — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль dcount_intersect в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7c66647dcdebfc86990c9a63f7404681b000b915
ms.sourcegitcommit: 8a7165b28ac6b40722186300c26002fb132e6e4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92749520"
---
# <a name="dcount_intersect-plugin"></a>Подключаемый модуль dcount_intersect

Вычисляет пересечение между N наборами на основе `hll` значений (N в диапазоне [2.. 16]) и возвращает N `dcount` значений.

Заданные наборы S<sub>1</sub>, s<sub>2</sub>,.. S<sub>n</sub> — возвращает значения, представляющие различные счетчики:  
S<sub>1</sub>,  
<sub>1</sub> ∩ s<sub>2</sub>,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩<sub>3</sub>,  
... ,  
S<sub>1</sub> ∩ s<sub>2</sub> ∩... ∩ S<sub>n</sub>

```kusto
T | evaluate dcount_intersect(hll_1, hll_2, hll_3)
```

## <a name="syntax"></a>Синтаксис

*T* `| evaluate` `dcount_intersect(` *hll_1* , *hll_2* , [ `,` *hll_3* `,` ...]`)`

## <a name="arguments"></a>Аргументы

* *T* : Входное табличное выражение.
* *hll_i* : значения <sub>Set S,</sub> вычисленные с помощью [`hll()`](./hll-aggfunction.md) функции.

## <a name="returns"></a>Возвращаемое значение

Возвращает таблицу со значениями N `dcount` (для каждого столбца), представляющими собой набор пересечения.
Имена столбцов: S0, S1,... (до n – 1).

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|евеннумберс|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|
