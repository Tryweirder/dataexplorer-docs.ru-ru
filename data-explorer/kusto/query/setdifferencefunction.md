---
title: set_difference () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается set_difference () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 7e13a9b652e1bdadb325cd866bddd78761b25b85
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372393"
---
# <a name="set_difference"></a>set_difference()

Возвращает `dynamic` массив (JSON) набора всех уникальных значений, которые находятся в первом массиве, но отсутствуют в других массивах (((arr1 \ arr2) \ arr3) \...).

**Синтаксис**

`set_difference(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

**Аргументы**

* *arr1... Аррн*: входные массивы для создания набора различий (по крайней мере два массива). Все аргументы должны быть динамическими массивами (см. [pack_array](packarrayfunction.md)). 

**Возвращает**

Возвращает динамический массив из набора всех уникальных значений, которые находятся в arr1, но отсутствуют в других массивах. См [`set_union()`](setunionfunction.md) . раздел и [`set_intersect()`](setintersectfunction.md) .

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(x,y,w)
| project set_difference(a1, a2, a3)
```

|Column1|
|---|
|[4]|
|8|
|Двенадцать|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|маленькая|
|---|
|[]|