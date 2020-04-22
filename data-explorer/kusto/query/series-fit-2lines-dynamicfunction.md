---
title: series_fit_2lines_dynamic () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны series_fit_2lines_dynamic () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb2743789c363a33e21ba472a945087b9b277cf
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663516"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

Применяет два сегмента линейной регрессии на серию, возвращая динамический объект.  

Принимает выражение, содержащее динамический числовой массив, в качестве входного и применяет [двухсегментную линейную регрессию](https://en.wikipedia.org/wiki/Segmented_regression) для выявления и количественной оценки изменения тренда в серии. Функция итерирует индексы серии и в каждой итерации разделяет серию на 2 части, подходит отдельной строке (с использованием [series_fit_line()](series-fit-linefunction.md) или [series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)) к каждой части и вычисляет общий r-квадрат. Лучший раскол является тот, который максимизировал r-квадрат; функция возвращает свои параметры в динамическом значении со следующим содержанием::
* `rsquare`: [r-квадрат](https://en.wikipedia.org/wiki/Coefficient_of_determination) является стандартной мерой качества подгонки. Это число в диапазоне [0–1], где 1 означает максимальное соответствие, а 0 означает, что данные абсолютно не упорядочены и не соответствуют какой-либо линии.
* `split_idx`: индекс переломной точки до 2 сегментов (на нулевой основе)
* `variance`: дисперсия входных данных
* `rvariance`: остаточная дисперсия, которая представляет собой дисперсию между значениями входных данных, приближенных (по сегментам 2 строки).
* `line_fit`: числовой массив, держащий ряд значений наиболее приспособленной линии. Длина ряда равна длине входного массива. Он главным образом используется для построения диаграмм.
* `right.rsquare`: r-квадрат линии на правой стороне сплита, [см. series_fit_line ()](series-fit-linefunction.md) или[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)
* `right.slope`: наклон правой приближенной линии (это от йакса)
* `right.interception`: перехват приблизительной левой линии (это b от y'ax'b)
* `right.variance`: дисперсия входных данных на правой стороне раскола
* `right.rvariance`: остаточная дисперсия входных данных на правой стороне раскола
* `left.rsquare`: r-квадрат линии на левой стороне сплита, см. [series_fit_line ()](series-fit-linefunction.md) или [series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)
* `left.slope`: наклон левой приближенной линии (это от йакса)
* `left.interception`: перехват приблизительной левой линии (это b от y'ax'b)
* `left.variance`: отклонение входных данных на левой стороне раскола
* `left.rvariance`: остаточная дисперсия входных данных на левой стороне раскола

Этот оператор похож на [series_fit_2lines,](series-fit-2linesfunction.md)но в отличие от `series-fit-2lines` него возвращает динамический мешок.

**Синтаксис**

`series_fit_2lines_dynamic(`*X*`)`

**Аргументы**

* *x*: Динамический массив числовых значений.  

> [!TIP]
> Наиболее удобным способом использования этой функции является ее применение к результатам оператора [make-series.](make-seriesoperator.md)

**Примеры**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="Серия подходит 2 линии":::
