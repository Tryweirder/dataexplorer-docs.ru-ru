---
title: set_intersect () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны set_intersect () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 0a1ef86573a408f644e26b3b23f0db42e327573a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507758"
---
# <a name="set_intersect"></a>set_intersect()

Возвращает `dynamic` массив (JSON) из набора всех различных значений, которые находятся во всех массивах - (arr1 и arr2 ...).

**Синтаксис**

`set_intersect(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**Аргументы**

* *arr1... arrN*: Входные массивы для создания набора пересекающихся (по крайней мере, двух массивов). Все аргументы должны быть динамическими массивами [(см. pack_array).](packarrayfunction.md) 

**Возвращает**

Возвращает динамический массив набора всех различных значений, которые находятся во всех массивах. Увидеть [`set_union()`](setunionfunction.md) [`set_difference()`](setdifferencefunction.md)и .

**Пример**

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
|[1]|
|[2]|
|[3]|

```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|Arr|
|---|
|[]|