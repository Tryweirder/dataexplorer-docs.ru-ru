---
title: series_stats () — обозреватель данных Azure
description: В этой статье описывается series_stats () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 5eb6bb2d11b35a7844a0366fc10797db621f6120
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241960"
---
# <a name="series_stats"></a>series_stats()

`series_stats()` Возвращает статистику для ряда в нескольких столбцах.  

`series_stats()`Функция принимает столбец, содержащий динамический числовой массив, в качестве входных данных и вычисляет следующие столбцы:
* `min`: минимальное значение во входном массиве
* `min_idx`: первое расположение минимального значения во входном массиве
* `max`: максимальное значение во входном массиве
* `max_idx`: первое расположение максимального значения во входном массиве
* `avg`: среднее значение входного массива.
* `variance`: выборка дисперсии входного массива
* `stdev`: пример стандартного отклонения входного массива

> [!NOTE] 
> Эта функция возвращает несколько столбцов, поэтому ее нельзя использовать в качестве аргумента для другой функции.

## <a name="syntax"></a>Синтаксис

проект `series_stats(` *x* `[,` *ignore_nonfinite* `])` или Extend `series_stats(` *x* `)` возвращает все упомянутые выше столбцы со следующими именами: series_stats_x_min, series_stats_x_min_idx и т. д.
 
Project (m, MI) = `series_stats(` *x* `)` или extend (m, MI) = `series_stats(` *x* `)` возвращает следующие столбцы: m (min) и MI (min_idx).

## <a name="arguments"></a>Аргументы

* *x*: ячейка динамического массива, представляющая собой массив числовых значений. 
* *ignore_nonfinite*: логический флаг (необязательный, по умолчанию: `false` ), указывающий, следует ли вычислять статистику при игнорировании неконечных значений (*null*, *NaN*, *INF*и т. д.). Если задано значение `false` , возвращаемые значения будут иметь значение, `null` Если в массиве имеются неконечные значения.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32,8|28.5036338535483|812.457142857143|
