---
title: array_split () — обозреватель данных Azure
description: В этой статье описывается array_split () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 102077c9c1116bd9476c6dae59d993a6379b69bd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225564"
---
# <a name="array_split"></a>array_split()

Разделяет массив на несколько массивов в соответствии с разбиением индексов и упаковывает созданный массив в динамический массив.

**Синтаксис**

`array_split`(*`arr`*, *`indices`*)

**Аргументы**

* *`arr`*: Входной массив для разбиения должен быть динамическим массивом.
* *`indices`*: Целочисленный или динамический массив целых чисел с разделенными индексами (от нуля), отрицательные значения преобразуются в array_length + value.

**Возвращает**

Динамический массив, содержащий N + 1 массива со значениями в диапазоне `[0..i1), [i1..i2), ... [iN..array_length)` от `arr` , где N — число входных индексов и `i1...iN` являются индексами.

**Примеры**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1, 2], [3, 4, 5]]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1, 2, 3, 4, 5]|[[1], [2, 3], [4, 5]]|
