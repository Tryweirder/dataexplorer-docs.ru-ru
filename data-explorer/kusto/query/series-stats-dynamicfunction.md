---
title: series_stats_dynamic () — обозреватель данных Azure
description: В этой статье описывается series_stats_dynamic () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 681e4e1b734fb9bf9d2357ec77be4ba1d61365dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245914"
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

## <a name="syntax"></a>Синтаксис

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite* x`])`

## <a name="arguments"></a>Аргументы

* *x*: ячейка динамического массива, представляющая собой массив числовых значений. 
* *ignore_nonfinite*: логический флаг (необязательный, по умолчанию: `false` ), указывающий, следует ли вычислять статистику при игнорировании неконечных значений (*null*, *NaN*, *INF*и т. д.). Если задано `false` возвращаемое значение, `null` в массиве содержатся неконечные значения.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min": 2,0, "min_idx": 8, "Max": 87,0, "max_idx": 3, "AVG": 32,8, "STDEV": 28.503633853548269, "вариативность": 812.45714285714291}
