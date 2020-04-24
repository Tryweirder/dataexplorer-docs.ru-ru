---
title: set_union () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается set_union () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: de9220ea6f9e23e458dd379fb164317842a48c94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507673"
---
# <a name="set_union"></a>set_union()

Возвращает `dynamic` массив (JSON) из набора всех различных значений, которые находятся в любом из массивов - (arr1 'arr2 '...).

**Синтаксис**

`set_union(`*arr1*`, `*arr2*`[`,` *arr3*, ...]``)`

**Аргументы**

* *arr1... arrN*: Ввод массивов для создания набора соединения (по крайней мере два массива). Все аргументы должны быть динамическими массивами [(см. pack_array).](packarrayfunction.md) 

**Возвращает**

Возвращает динамический массив набора всех различных значений, которые находятся в любом из массивов. Увидеть [`set_intersect()`](setintersectfunction.md) [`set_difference()`](setdifferencefunction.md)и .

**Пример**

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
|[1,2,4,8]|
|[2,4,8,16]|
|[3,6,12,24]|