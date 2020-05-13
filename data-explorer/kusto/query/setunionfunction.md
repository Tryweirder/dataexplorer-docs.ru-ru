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
ms.openlocfilehash: 7e719ffa448ce3060a0a520de2c031b7d4b7d109
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372338"
---
# <a name="set_union"></a>set_union()

Возвращает `dynamic` массив (JSON) набора всех различных значений, которые находятся в любом из массивов (arr1 ∪ arr2 ∪...).

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
