---
title: series_fit_2lines_dynamic () — обозреватель данных Azure
description: В этой статье описывается series_fit_2lines_dynamic () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 85a034751c6e29b2ca78f1df9de045c1ac07c7fa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87343912"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

Применяет к ряду линейную регрессию с двумя сегментами, возвращая динамический объект.  

Принимает выражение, содержащее динамический числовой массив, в качестве входных данных и применяет [линейную регрессию двух сегментов](https://en.wikipedia.org/wiki/Segmented_regression) для выявления и количественных изменений тенденций в ряде. Функция выполняет итерацию по индексам рядов. В каждой итерации ряд разбивается на две части и подразделяется на отдельную строку с помощью [series_fit_line ()](series-fit-linefunction.md) или [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md). Функция соответствует линиям каждой из двух частей и вычисляет общее значение R-квадрат. Лучшим разбиением является то, что позволяет максимально увеличить значение R-квадрат. Функция возвращает свои параметры в динамическом значении со следующим содержимым:

* `rsquare`: [R-квадрат](https://en.wikipedia.org/wiki/Coefficient_of_determination) — это стандартная мера качества подгонки. Это число в диапазоне [0-1], где 1 является лучшим подгонкум, а 0 означает, что данные не упорядочены и не соответствуют ни одной строке.
* `split_idx`: индекс точки останова, указывающий на два сегмента (от нуля).
* `variance`: дисперсия входных данных.
* `rvariance`: Остаточная дисперсия, которая является дисперсией между значениями входных данных, приблизительными (по двум линейным сегментам).
* `line_fit`: числовой массив, содержащий ряд значений наиболее подходящей строки. Длина ряда равна длине входного массива. Он используется для построения диаграмм.
* `right.rsquare`: r-квадрат линии с правой стороны разбиения, см. раздел [series_fit_line ()](series-fit-linefunction.md) или [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md).
* `right.slope`: наклон правой строки приблизительного приближения (для вида y = ax + b).
* `right.interception`: перехват приблизительной левой строки (b от y = ax + b).
* `right.variance`: дисперсия входных данных с правой стороны разбиения.
* `right.rvariance`: Остаточная дисперсия входных данных с правой стороны разбиения.
* `left.rsquare`: r-Square линии слева от разбиения см. в разделе [series_fit_line ()]. (series-fit-linefunction.md) или [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md).
* `left.slope`: наклон левой строки с приближением к левому краю (для вида y = ax + b).
* `left.interception`: перехват приблизительной левой строки (для вида y = ax + b).
* `left.variance`: дисперсия входных данных с левой стороны разбиения.
* `left.rvariance`: Остаточная дисперсия входных данных с левой стороны разбиения.

Этот оператор аналогичен [series_fit_2lines](series-fit-2linesfunction.md). В отличие от `series-fit-2lines` , она возвращает динамический контейнер.

## <a name="syntax"></a>Синтаксис

`series_fit_2lines_dynamic(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: динамический массив числовых значений.  

> [!TIP]
> Наиболее удобный способ использования этой функции — применение его к результатам оператора [make-Series](make-seriesoperator.md) .

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Ряды соответствуют 2 строкам":::
