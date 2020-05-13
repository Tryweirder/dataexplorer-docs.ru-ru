---
title: series_stats_dynamic () — обозреватель данных Azure
description: В этой статье описывается series_stats_dynamic () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 87cee5244fb1276733d4cf44d0477cc3351b947c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372454"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

Возвращает статистику для ряда в динамическом объекте.  

`series_stats_dynamic()`Функция принимает столбец, содержащий динамический числовой массив, в качестве входных данных и создает динамическое значение со следующим содержимым:
* `min`: минимальное значение во входном массиве
* `min_idx`: первое расположение минимального значения во входном массиве
* `max`: максимальное значение во входном массиве
* `max_idx`: первое расположение максимального значения во входном массиве
* `avg`: среднее значение входного массива.
* `variance`: выборка дисперсии входного массива
* `stdev`: пример стандартного отклонения входного массива

**Синтаксис**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite* x`])`

**Аргументы**

* *x*: ячейка динамического массива, представляющая собой массив числовых значений. 
* *ignore_nonfinite*: логический флаг (необязательный, по умолчанию: `false` ), указывающий, следует ли вычислять статистику при игнорировании неконечных значений (*null*, *NaN*, *INF*и т. д.). Если задано `false` возвращаемое значение, `null` в массиве содержатся неконечные значения.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min": 2,0, "min_idx": 8, "Max": 87,0, "max_idx": 3, "AVG": 32,8, "STDEV": 28.503633853548269, "вариативность": 812.45714285714291}
