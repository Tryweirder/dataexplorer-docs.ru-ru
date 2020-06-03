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
ms.openlocfilehash: 612829f2cbcd8b3f495d516b254faf7a9cf6919a
ms.sourcegitcommit: 02236d1f23f48f9dd41cc7433f46991356a869fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84306577"
---
# <a name="array_slice"></a>array_slice()

Извлекает срез динамического массива.

**Синтаксис**

`array_slice`(*`arr`*, *`start`*, *`end`*)

**Аргументы**

* *`arr`*: Входной массив для извлечения среза должен быть динамическим массивом.
* *`start`*: Отсчитываемый от нуля (включительно) начальный индекс среза, отрицательные значения преобразуются в array_length + Start.
* *`end`*: Отсчитываемый от нуля (включительно) Конечный индекс среза, отрицательные значения преобразуются в array_length + End.

Примечание. индексы вне границ игнорируются.

**Возвращает**

Динамический массив значений в диапазоне [ `start..end` ] из `arr` .

**Примеры**

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
