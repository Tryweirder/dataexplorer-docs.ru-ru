---
title: set_difference () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны set_difference () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: d4edb8ec46fca99b7dd58b11bbd54442a9340c7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507809"
---
# <a name="set_difference"></a>set_difference()

Возвращает `dynamic` массив (JSON) из набора всех различных значений, которые находятся в первом массиве, но не в других массивах - (((arr1 й arr2)

**Синтаксис**

`set_difference(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**Аргументы**

* *arr1... arrN*: Ввод массивов для создания набора различий (по крайней мере два массива). Все аргументы должны быть динамическими массивами [(см. pack_array).](packarrayfunction.md) 

**Возвращает**

Возвращает динамический массив набора всех различных значений, которые находятся в arr1, но не находятся в других массивах. Увидеть [`set_union()`](setunionfunction.md) [`set_intersect()`](setintersectfunction.md)и .

**Пример**

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
|[8]|
|[12]|

```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|Arr|
|---|
|[]|