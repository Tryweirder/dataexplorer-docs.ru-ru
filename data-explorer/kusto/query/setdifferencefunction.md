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
ms.openlocfilehash: bd442f70b3411b61f213098fefec918622f8d916
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351188"
---
# <a name="set_difference"></a>set_difference()

Возвращает `dynamic` массив (JSON) набора всех уникальных значений, которые находятся в первом массиве, но отсутствуют в других массивах (((arr1 \ arr2) \ arr3) \...).

## <a name="syntax"></a>Синтаксис

`set_difference(`*arr1* `, ` *arr2* `[` ,` *arr3*, ...])`

## <a name="arguments"></a>Аргументы

* *arr1... Аррн*: входные массивы для создания набора различий (по крайней мере два массива). Все аргументы должны быть динамическими массивами (см. [pack_array](packarrayfunction.md)). 

## <a name="returns"></a>Результаты

Возвращает динамический массив из набора всех уникальных значений, которые находятся в arr1, но отсутствуют в других массивах. См [`set_union()`](setunionfunction.md) . раздел и [`set_intersect()`](setintersectfunction.md) .

## <a name="example"></a>Пример

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
| [12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|маленькая|
|---|
|[]|