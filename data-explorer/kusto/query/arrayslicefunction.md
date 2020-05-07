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
ms.openlocfilehash: f2de8d91b73a3495c8b0902d710bd87c7fecbafa
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737561"
---
# <a name="array_slice"></a>array_slice()

Извлекает срез динамического массива.

**Синтаксис**

`array_slice`(*`arr`*, *`start`*, *`end`*])

**Аргументы**

* *`arr`*: Входной массив для извлечения среза должен быть динамическим массивом.
* *`start`*: Отсчитываемый от нуля (включительно) начальный индекс среза, отрицательные значения преобразуются в array_length + Start.
* *`end`*: Отсчитываемый от нуля (включительно) Конечный индекс среза, отрицательные значения преобразуются в array_length + End.

Примечание. индексы вне границ игнорируются.

**Возвращает**

Динамический массив значений в диапазоне [`start..end`] из. `arr`

**Примеры**


```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|`arr`|`sliced`|
|---|---|
|[1, 2, 3]|[2, 3]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|`arr`|обрезаны|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4, 5]|


```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|`arr`|обрезаны|
|---|---|
|[1, 2, 3, 4, 5]|[3, 4]|