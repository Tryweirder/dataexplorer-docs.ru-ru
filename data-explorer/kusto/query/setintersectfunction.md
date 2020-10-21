---
title: set_intersect () — обозреватель данных Azure
description: В этой статье описывается set_intersect () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 682cff2dc5a4334a4543767048429b1ea04dc329
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242451"
---
# <a name="set_intersect"></a>set_intersect()

Возвращает `dynamic` массив из набора всех различных значений, которые находятся во всех массивах (arr1 ∩ arr2 ∩...).

## <a name="syntax"></a>Синтаксис

`set_intersect(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

## <a name="arguments"></a>Аргументы

* *arr1... Аррн*: входные массивы для создания пересекающихся наборов (по крайней мере два массива). Все аргументы должны быть динамическими массивами. Дополнительные сведения см. в разделе [pack_array](packarrayfunction.md). 

## <a name="returns"></a>Результаты

Возвращает динамический массив набора всех различных значений, которые находятся во всех массивах. См [`set_union()`](setunionfunction.md) . раздел и [`set_difference()`](setdifferencefunction.md) .

## <a name="example"></a>Пример

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
