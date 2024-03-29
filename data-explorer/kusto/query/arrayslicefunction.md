---
title: array_slice () — обозреватель данных Azure
description: В этой статье описывается array_slice () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ecb30d00a6c95e3754686eb264d9439c1c1e605f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246764"
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
