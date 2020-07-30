---
title: array_slice () — обозреватель данных Azure
description: В этой статье описывается array_slice () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: e2216361022f055078be66f37f3d2b084afaa4c6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349539"
---
# <a name="array_slice"></a>array_slice()

Извлекает срез динамического массива.

## <a name="syntax"></a>Синтаксис

`array_slice`(*`arr`*, *`start`*, *`end`*)

## <a name="arguments"></a>Аргументы

* *`arr`*: Входной массив для извлечения среза должен быть динамическим массивом.
* *`start`*: Отсчитываемый от нуля (включительно) начальный индекс среза, отрицательные значения преобразуются в array_length + Start.
* *`end`*: Отсчитываемый от нуля (включительно) Конечный индекс среза, отрицательные значения преобразуются в array_length + End.

Примечание. индексы вне границ игнорируются.

## <a name="returns"></a>Результаты

Динамический массив значений в диапазоне [ `start..end` ] из `arr` .

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2, 3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|обрезаны|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4, 5]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|обрезаны|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4]|
