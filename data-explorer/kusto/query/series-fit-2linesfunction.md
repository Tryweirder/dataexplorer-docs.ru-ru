---
title: series_fit_2lines () — обозреватель данных Azure
description: В этой статье описывается series_fit_2lines () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 89e93a3c4365e0f215a797ee0b01669e6bcbdcfa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87343895"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Применяет к ряду линейную регрессию с двумя сегментами, возвращая несколько столбцов.  

Принимает выражение, содержащее динамический числовой массив, в качестве входных данных и применяет [линейную регрессию двух сегментов](https://en.wikipedia.org/wiki/Segmented_regression) , чтобы определить и количественно оценить изменение тенденций в ряде. Функция выполняет итерацию по индексам рядов. В каждой итерации функция разделяет ряды на две части, подсчитывает отдельную строку (с помощью [series_fit_line ()](series-fit-linefunction.md)) к каждой части и вычисляет общий r-квадрат. Наилучшее разделение характеризуется максимальным значением R-квадрата. Функция возвращает следующие параметры.


|Параметр  |Описание  |
|---------|---------|
|`rsquare`     | [R-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) — это стандартная мера качества подгонки. Это число в диапазоне [0-1], где 1 — наилучшее соответствие, а 0 означает, что данные не упорядочены и не соответствуют ни одной строке.        |
|`split_idx`     |   Индекс точки останова, указывающий на два сегмента (от нуля).      |
|`variance`     | Дисперсия входных данных.        |
|`rvariance`     | Остаточная дисперсия, которая является дисперсией между значениями входных данных, приблизительными (по двум линейным сегментам).        |
|`line_fit`     | Числовой массив, содержащий ряд значений наиболее подходящей строки. Длина ряда равна длине входного массива. Он главным образом используется для построения диаграмм.        |
|`right_rsquare`     | R-квадрат линии с правой стороны разбиения см. в разделе [series_fit_line ()](series-fit-linefunction.md).        |
|`right_slope`     | Наклон правой приближенной строки (для вида y = ax + b).         |
|`right_interception`     |  Перехват приблизительной левой строки (b от y = ax + b).       |
|`right_variance`    | Дисперсия входных данных с правой стороны разбиения.        |
|`right_rvariance`     | Остаточная дисперсия входных данных с правой стороны разбиения.        |
|`left_rsquare`     | R-квадрат линии с левой стороны разбиения, см. раздел [series_fit_line ()](series-fit-linefunction.md).        |
|`left_slope`    | Наклон влево приближенной строки (для вида y = ax + b).        |
|`left_interception`     |   Перехват приблизительной левой строки (для вида y = ax + b).      |
|`left_variance`     | Дисперсия входных данных с левой стороны разбиения.        |
|`left_rvariance`     | Остаточная дисперсия входных данных с левой стороны разбиения.        |


> [!Note]
> Эта функция возвращает несколько столбцов, поэтому не может использоваться в качестве аргумента для другой функции.

## <a name="syntax"></a>Синтаксис

проект `series_fit_2lines(` *x*`)`
* Возвратит все упомянутые выше столбцы со следующими именами: series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx и т. д.

Project (RS, Si, v) = `series_fit_2lines(` *x*`)`
* Возвращает следующие столбцы: RS (r-Square), Si (разделение индекса), v (вариативность) и остальное будет выглядеть как series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit и т. д.

extend (RS, Si, v) = `series_fit_2lines(` *x*`)`
* Возвращает только: rs (R-квадрат), si (индекс разбиения) и v (дисперсия).
  
## <a name="arguments"></a>Аргументы

* *x*: динамический массив числовых значений.  

> [!TIP]
> Наиболее удобный способ использования этой функции — применение ее к результатам оператора [make-Series](make-seriesoperator.md) .

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Ряды соответствуют 2 строкам":::
