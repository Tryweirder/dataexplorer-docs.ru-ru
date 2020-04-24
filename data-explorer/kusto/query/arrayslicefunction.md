---
title: array_slice() - Исследователь данных Azure (англ.) Документы Майкрософт
description: Эта статья описывает array_slice () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/03/2018
ms.openlocfilehash: ed5c320ef639f7545e19bcaabd9b53f4424c959d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518587"
---
# <a name="array_slice"></a>array_slice()

Извлекает кусочек динамического массива.

**Синтаксис**

`array_slice(`*arr,* *начать,* *закончить*`)`

**Аргументы**

* *arr*: Ввод массива для извлечения ломтик из, должен быть динамический массив.
* *начало*: нулевой (включительно) стартовый индекс среза, отрицательные значения преобразуются в array_length старт.
* *конец*: нулевой (включительно) конечный индекс среза, отрицательные значения преобразуются в array_length.end.

Примечание: за пределами индексов игнорируются.

**Возвращает**

Динамический массив значений в диапазоне «старт. конец от arr.

**Примеры**

1.
```kusto
print arr=dynamic([1,2,3]) 
| extend sliced=array_slice(arr, 1, 2)
```
|Arr|Нарезанный|
|---|---|
|[1,2,3]|[2,3]|


2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, 2, -1)
```
|Arr|Нарезанный|
|---|---|
|[1,2,3,4,5]|[3,4,5]|


3.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend sliced=array_slice(arr, -3, -2)
```
|Arr|Нарезанный|
|---|---|
|[1,2,3,4,5]|[3,4]|