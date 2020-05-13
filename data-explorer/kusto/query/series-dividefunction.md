---
title: series_divide () — обозреватель данных Azure
description: В этой статье описывается series_divide () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7d5bdba030687c17c355eb72ce2fc9c358c10ebd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372850"
---
# <a name="series_divide"></a>series_divide()

Вычисляет поэлементное деление двух входных числовых рядов.

**Синтаксис**

`series_divide(`*Series1* `,` *Series2*`)`

**Аргументы**

* *Series1, Series2*: входные числовые массивы, первый из которых будет полагаться на поэлементное деление на секунду в результате динамического массива. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив вычисляемой операции деления на уровне элементов между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

Примечание. результирующий ряд имеет тип Double, даже если входные данные являются целыми числами. Деление на ноль следует за двойным делением на ноль (например, 2/0 возвращает значение Double (+ INF)).

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [0,25, 1.0, 4.0]|
|[2, 4, 8]    |[8, 4, 2]|   [0,25, 1.0, 4.0]|
|[3, 6, 12]   |[12, 6, 3]|  [0,25, 1.0, 4.0]|
