---
title: set_union () — обозреватель данных Azure
description: В этой статье описывается set_union () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 19c7b47318d0748510aba146968149cd0eb246a2
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264578"
---
# <a name="set_union"></a>set_union()

Возвращает `dynamic` массив из набора всех различных значений, которые находятся в любом из массивов (arr1 ∪ arr2 ∪...).

**Синтаксис**

`set_union(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...]``)`

**Аргументы**

* *arr1... Аррн*: входные массивы для создания набора Union (по крайней мере двух массивов). Все аргументы должны быть динамическими массивами (см. [pack_array](packarrayfunction.md)). 

**Возвращает**

Возвращает динамический массив набора всех различных значений, которые находятся в любом из массивов. См [`set_intersect()`](setintersectfunction.md) . раздел и [`set_difference()`](setdifferencefunction.md) .

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|Column1|
|---|
|[1, 2, 4, 8]|
|[2, 4, 8, 16]|
|[3, 6, 12, 24]|
