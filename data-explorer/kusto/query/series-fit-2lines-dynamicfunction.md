---
title: series_fit_2lines_dynamic () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается series_fit_2lines_dynamic () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e2671b112ff1a73a5f9bbc10d6537a5e8d7b755e
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618774"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

Применяет к ряду линейную регрессию с двумя сегментами, возвращая динамический объект.  

Принимает выражение, содержащее динамический числовой массив, в качестве входных данных и применяет [линейную регрессию двух сегментов](https://en.wikipedia.org/wiki/Segmented_regression) для определения и количественного изменения тенденций в ряде. Функция выполняет итерацию по индексам рядов и в каждой итерации разделяет ряды на 2 части, подбирает отдельную строку (с помощью [series_fit_line ()](series-fit-linefunction.md) или [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)) к каждой части и вычисляет общее значение r-Square. Наилучшим разбиением является то, что разворачивает r-квадрат; функция возвращает свои параметры в динамическом значении со следующим содержимым:
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) — стандартная мера качества подгонки. Это число в диапазоне [0–1], где 1 означает максимальное соответствие, а 0 означает, что данные абсолютно не упорядочены и не соответствуют какой-либо линии.
* `split_idx`: индекс от точки останова до 2 сегментов (от нуля)
* `variance`: дисперсия входных данных
* `rvariance`: Остаточная дисперсия, которая является дисперсией между значениями входных данных приблизительного (по 2 сегментам линии).
* `line_fit`: числовой массив, содержащий ряд значений наиболее подходящей строки. Длина ряда равна длине входного массива. Он главным образом используется для построения диаграмм.
* `right.rsquare`: r-квадрат линии с правой стороны разбиения, см. раздел [series_fit_line ()](series-fit-linefunction.md) или[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md) .
* `right.slope`: наклон правой строки приблизительного приближения (это от y = ax + b)
* `right.interception`: перехват приблизительной левой строки (это б от y = ax + b)
* `right.variance`: дисперсия входных данных с правой стороны разбиения
* `right.rvariance`: Остаточная дисперсия входных данных с правой стороны разбиения
* `left.rsquare`: r-Square линии слева от разбиения см. в разделе [series_fit_line ()](series-fit-linefunction.md) или [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md) .
* `left.slope`: наклон левой строки приближения (это от y = ax + b)
* `left.interception`: перехват приблизительной левой строки (это б от y = ax + b)
* `left.variance`: дисперсия входных данных в левой части разбиения
* `left.rvariance`: Остаточная дисперсия входных данных с левой стороны разбиения

Этот оператор аналогичен [series_fit_2lines](series-fit-2linesfunction.md), но в отличие от `series-fit-2lines` него возвращает динамический контейнер.

**Синтаксис**

`series_fit_2lines_dynamic(`*x*`)`

**Аргументы**

* *x*: динамический массив числовых значений.  

> [!TIP]
> Наиболее удобный способ использования этой функции — применение ее к результатам оператора [make-Series](make-seriesoperator.md) .

**Примеры**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="Ряды соответствуют 2 строкам":::
