---
title: set_intersect () — обозреватель данных Azure
description: В этой статье описывается set_intersect () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 6739b01f996c04829a1fdb78eab96f6e51b011bb
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763673"
---
# <a name="set_intersect"></a>set_intersect()

Возвращает `dynamic` массив из набора всех различных значений, которые находятся во всех массивах (arr1 ∩ arr2 ∩...).

**Синтаксис**

`set_intersect(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

**Аргументы**

* *arr1... Аррн*: входные массивы для создания пересекающихся наборов (по крайней мере два массива). Все аргументы должны быть динамическими массивами. Дополнительные сведения см. в разделе [pack_array](packarrayfunction.md). 

**Возвращает**

Возвращает динамический массив набора всех различных значений, которые находятся во всех массивах. См [`set_union()`](setunionfunction.md) . раздел и [`set_difference()`](setdifferencefunction.md) .

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|Column1|
|---|
| [1]|
|открыт|
|[3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|маленькая|
|---|
|[]|
