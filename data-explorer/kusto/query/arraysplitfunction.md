---
title: array_split () — обозреватель данных Azure
description: В этой статье описывается array_split () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: b1dad77bd58d64bfcd167cc7d30f0986a54f13c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252795"
---
# <a name="array_split"></a>array_split()

Разделяет массив на несколько массивов в соответствии с разбиением индексов и упаковывает созданный массив в динамический массив.

## <a name="syntax"></a>Синтаксис

`array_split`(*`arr`*, *`indices`*)

## <a name="arguments"></a>Аргументы

* *`arr`*: Входной массив для разбиения должен быть динамическим массивом.
* *`indices`*: Целочисленный или динамический массив целых чисел с разделенными индексами (от нуля), отрицательные значения преобразуются в array_length + value.

## <a name="returns"></a>Результаты

Динамический массив, содержащий N + 1 массива со значениями в диапазоне `[0..i1), [i1..i2), ... [iN..array_length)` от `arr` , где N — число входных индексов и `i1...iN` являются индексами.

## <a name="examples"></a>Примеры

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
