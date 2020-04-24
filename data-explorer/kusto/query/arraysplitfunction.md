---
title: array_split () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны array_split () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 4d5d8ce5e918f335f1f26f4e3fc045ab0fa6e3a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518570"
---
# <a name="array_split"></a>array_split()

Разделит массив на несколько массивов в соответствии с разделенными индексами и упаковывает генерируемый массив в динамический массив.

**Синтаксис**

`array_split(`*arr*, *индексы*`)`

**Аргументы**

* *arr*: Ввод массива разделить, должен быть динамический массив.
* *индексы*: Целый или динамический массив целых индексов с разделенными индексами (на основе нуля), отрицательные значения преобразуются в array_length значение.

**Возвращает**

Динамический массив, содержащий массивы N-1 с значениями в диапазоне 0..i1, qi1. i2), ... ЗиН. array_length) от arr, где N является число входных индексов и i1. iN являются индексами.

**Примеры**

1.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1,2],[3,4,5]]|



2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1],[2,3],[4,5]]|