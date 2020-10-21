---
title: set_union () — обозреватель данных Azure
description: В этой статье описывается set_union () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 2fff5763f7eba13e48d0cbdb0e85af666c385308
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242624"
---
# <a name="set_union"></a>set_union()

Возвращает `dynamic` массив из набора всех различных значений, которые находятся в любом из массивов (arr1 ∪ arr2 ∪...).

## <a name="syntax"></a>Синтаксис

`set_union(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...]``)`

## <a name="arguments"></a>Аргументы

* *arr1... Аррн*: входные массивы для создания набора Union (по крайней мере двух массивов). Все аргументы должны быть динамическими массивами (см. [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Результаты

Возвращает динамический массив набора всех различных значений, которые находятся в любом из массивов. См [`set_intersect()`](setintersectfunction.md)  . раздел и [`set_difference()`](setdifferencefunction.md) .

## <a name="example"></a>Пример

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
