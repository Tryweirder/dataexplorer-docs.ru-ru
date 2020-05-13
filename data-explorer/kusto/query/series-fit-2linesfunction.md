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
ms.openlocfilehash: d4b4be37f171439b47399ecfbb314b1a9b704afd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372710"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Применяет к ряду линейную регрессию с двумя сегментами, возвращая несколько столбцов.  

Принимает выражение, содержащее динамический числовой массив, в качестве входных данных и применяет [линейную регрессию двух сегментов](https://en.wikipedia.org/wiki/Segmented_regression) для определения и количественного изменения тенденций в ряде. Функция выполняет итерацию по индексам рядов и в каждой итерации разделяет ряды на 2 части, подбирает отдельную строку (с помощью [series_fit_line ()](series-fit-linefunction.md)) к каждой части и вычисляет общий r-квадрат. Наилучшее разделение характеризуется максимальным значением R-квадрата. Функция возвращает следующие параметры.
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) — стандартная мера качества подгонки. Это число в диапазоне [0–1], где 1 означает максимальное соответствие, а 0 означает, что данные абсолютно не упорядочены и не соответствуют какой-либо линии.
* `split_idx`: индекс от точки останова до 2 сегментов (от нуля)
* `variance`: дисперсия входных данных
* `rvariance`: Остаточная дисперсия, которая является дисперсией между значениями входных данных приблизительного (по 2 сегментам линии).
* `line_fit`: числовой массив, содержащий ряд значений наиболее подходящей строки. Длина ряда равна длине входного массива. Он главным образом используется для построения диаграмм.
* `right_rsquare`: r-квадрат линии с правой стороны разбиения, см. раздел [series_fit_line ()](series-fit-linefunction.md) .
* `right_slope`: наклон правой строки приблизительного приближения (это от y = ax + b)
* `right_interception`: перехват приблизительной левой строки (это б от y = ax + b)
* `right_variance`: дисперсия входных данных с правой стороны разбиения
* `right_rvariance`: Остаточная дисперсия входных данных с правой стороны разбиения
* `left_rsquare`: r-Square линии слева от разбиения см. в разделе [series_fit_line ()](series-fit-linefunction.md) .
* `left_slope`: наклон левой строки приближения (это от y = ax + b)
* `left_interception`: перехват приблизительной левой строки (это б от y = ax + b)
* `left_variance`: дисперсия входных данных в левой части разбиения
* `left_rvariance`: Остаточная дисперсия входных данных с левой стороны разбиения

*Обратите внимание* , что эта функция возвращает несколько столбцов, поэтому ее нельзя использовать в качестве аргумента для другой функции.

**Синтаксис**

проект `series_fit_2lines(` *x*`)`
* Возвратит все упомянутые выше столбцы со следующими именами: series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx и т. д.
Project (RS, Si, v) = `series_fit_2lines(` *x*`)`
* Возвращает следующие столбцы: RS (r-Square), Si (разделение индекса), v (вариативность), а остальное будет выглядеть как series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit и т. д. расширения (RS, Si, v) = `series_fit_2lines(` *x*`)`
* Возвращает только: rs (R-квадрат), si (индекс разбиения) и v (дисперсия).
  
**Аргументы**

* *x*: динамический массив числовых значений.  

> [!TIP]
> Наиболее удобный способ использования этой функции — применение ее к результатам оператора [make-Series](make-seriesoperator.md) .

**Примеры**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Ряды соответствуют 2 строкам":::
