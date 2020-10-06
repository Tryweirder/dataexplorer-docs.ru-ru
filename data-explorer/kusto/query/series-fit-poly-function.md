---
title: series_fit_poly () — обозреватель данных Azure
description: В этой статье описывается series_fit_poly () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/21/2020
ms.openlocfilehash: 68f9f55b1ff0c66bb5d50e624f9063d7e177f50a
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771824"
---
# <a name="series_fit_poly"></a>series_fit_poly ()

Применяет регрессию полинома из независимой переменной (x_series) к зависимой переменной (y_series). Эта функция принимает таблицу, содержащую несколько рядов (динамические числовые массивы), и формирует наилучшее значение степени соответствия высокого порядка для каждого ряда, используя [регрессию полинома](https://en.wikipedia.org/wiki/Polynomial_regression). 

> [!TIP]
> * Для линейной регрессии для ряда с равномерной пробелом, созданной [оператором make-Series](make-seriesoperator.md), используйте более простую функцию [series_fit_line ()](series-fit-linefunction.md). См. [Пример 2](#example-2).
> * Если передается *x_series* , а регрессия выполняется в более высоком уровне, рассмотрите возможность нормализации до диапазона [0-1]. См. [Пример 3](#example-3).
> * Если *x_series* имеет тип DateTime, его необходимо преобразовать в Double и нормализацию. См. [Пример 3](#example-3).
> * Дополнительные сведения о реализации многополиномной регрессии с помощью встроенного кода Python см. в разделе [series_fit_poly_fl ()](../functions-library/series-fit-poly-fl.md).


## <a name="syntax"></a>Синтаксис

`T | extend  series_fit_poly(`*y_series* `, ` *x_series* `, ` *степень*`)`
  
## <a name="arguments"></a>Аргументы

|Аргумент| Описание| Обязательный или необязательный| Примечания|
|---|---|---|---|
| *y_series* | Динамический числовой массив, содержащий [зависимую переменную](https://en.wikipedia.org/wiki/Dependent_and_independent_variables). | Обязательно |
| *x_series* | Динамический числовой массив, содержащий [независимую переменную](https://en.wikipedia.org/wiki/Dependent_and_independent_variables). | Необязательный элемент. Требуется только для [неравномерного пробельного ряда](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series). | Если этот параметр не задан, то ему присваивается значение по умолчанию [1, 2,..., длина (y_series)].|
| *иной* | Необходимый порядок полинома для размещения. Например, 1 для линейной регрессии, 2 для квадратичной регрессии и т. д. | Необязательно | По умолчанию используется значение 1 (линейная регрессия).|

## <a name="returns"></a>Возвращаемое значение

`series_fit_poly()`Функция возвращает следующие столбцы:

* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) — стандартная мера качества подгонки. Значение в диапазоне [0-1], где 1 — наилучшее соответствие, а 0 означает, что данные не упорядочены и не соответствуют ни одной строке.
* `coefficients`: Числовой массив, содержащий коэффициенты оптимального коэффициента с заданной степенью, упорядоченный от самого высокого степени.
* `variance`: Дисперсия зависимой переменной (y_series).
* `rvariance`: Остаточная дисперсия, которая является дисперсией между значениями входных данных приблизительного значения.
* `poly_fit`: Числовой массив, содержащий ряд значений с оптимальным значением полинома. Длина ряда равна длине зависимой переменной (y_series). Значение, используемое для построения диаграмм.

## <a name="examples"></a>Примеры

### <a name="example-1"></a>Пример 1

Пятый порядок, с пропускаемым по осям x & y:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend series_fit_poly(y, x, 5)
| project-rename fy=series_fit_poly_y_poly_fit, coeff=series_fit_poly_y_coefficients
|fork (project x, y, fy) (project-away x, y, fy)
| render linechart 
```

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-1.png" alt-text="Диаграмма, показывающая пятое значение полинома для ряда с шумом":::

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-table-1.png" alt-text="Диаграмма, показывающая пятое значение полинома для ряда с шумом" border="false":::

### <a name="example-2"></a>Пример 2

Убедитесь, что `series_fit_poly` в параметре с градусом = 1 совпадений `series_fit_line` :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_series1
| extend series_fit_line(y)
| extend series_fit_poly(y)
| project-rename y_line = series_fit_line_y_line_fit, y_poly = series_fit_poly_y_poly_fit
| fork (project x, y, y_line, y_poly) (project-away id, x, y, y_line, y_poly) 
| render linechart with(xcolumn=x, ycolumns=y, y_line, y_poly)
```

:::image type="content" source="images/series-fit-poly-function/fit-poly-line.png" alt-text="Диаграмма, показывающая пятое значение полинома для ряда с шумом":::

:::image type="content" source="images/series-fit-poly-function/fit-poly-line-table.png" alt-text="Диаграмма, показывающая пятое значение полинома для ряда с шумом" border="false":::
    
### <a name="example-3"></a>Пример 3

Неравномерные (неравномерное) временные ряды:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  x-axis must be normalized to the range [0-1] if either degree is relatively big (>= 5) or original x range is big.
//  so if x is a time axis it must be normalized as conversion of timestamp to long generate huge numbers (number of 100 nano-sec ticks from 1/1/1970)
//
//  Normalization: x_norm = (x - min(x))/(max(x) - min(x))
//
irregular_ts
| extend series_stats(series_add(TimeStamp, 0))                                                                 //  extract min/max of time axis as doubles
| extend x = series_divide(series_subtract(TimeStamp, series_stats__min), series_stats__max-series_stats__min)  // normalize time axis to [0-1] range
| extend series_fit_poly(num, x, 8)
| project-rename fnum=series_fit_poly_num_poly_fit
| render timechart with(ycolumns=num, fnum)
```
:::image type="content" source="images/series-fit-poly-function/irregular-time-series-1.png" alt-text="Диаграмма, показывающая пятое значение полинома для ряда с шумом":::
