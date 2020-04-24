---
title: series_multiply() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_multiply () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: fa000d1058730e0232790e7f0e3976fa203519c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508217"
---
# <a name="series_multiply"></a>series_multiply()

Вычисляет размножение элементов двух численных вводов серии.

**Синтаксис**

`series_multiply(`*серия1* `,` *серия2*`)`

**Аргументы**

* *series1, series2*: Числовые массивы ввода, чтобы быть помноженными элементами в результате динамического массива. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив расчетливой операции умножения элементов между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [4,4,4]|
|[2,4,8]    |[8,4,2]|   [16,16,16]|
|[3,6,12]   |[12,6,3]|  [36,36,36]|