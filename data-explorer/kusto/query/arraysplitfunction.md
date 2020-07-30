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
ms.openlocfilehash: be993f3b0a58b56b9b4d171378bf71a645e77f1a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349522"
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
