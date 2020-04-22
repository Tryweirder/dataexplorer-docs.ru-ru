---
title: series_fit_2lines () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны series_fit_2lines () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 05163735c88c3229c13d76e3f8fde9bff091b239
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663507"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

Применяет два сегмента линейной регрессии в серии, возвращая несколько столбцов.  

Принимает выражение, содержащее динамический числовой массив, в качестве входного и применяет [двухсегментную линейную регрессию](https://en.wikipedia.org/wiki/Segmented_regression) для выявления и количественной оценки изменения тренда в серии. Функция итерирует индексы серии и в каждой итерации разделяет серию на 2 части, подходит отдельной строке (с использованием [series_fit_line))](series-fit-linefunction.md)к каждой части и вычисляет общий r-квадрат. Наилучшее разделение характеризуется максимальным значением R-квадрата. Функция возвращает следующие параметры.
* `rsquare`: [r-квадрат](https://en.wikipedia.org/wiki/Coefficient_of_determination) является стандартной мерой качества подгонки. Это число в диапазоне [0–1], где 1 означает максимальное соответствие, а 0 означает, что данные абсолютно не упорядочены и не соответствуют какой-либо линии.
* `split_idx`: индекс переломной точки до 2 сегментов (на нулевой основе)
* `variance`: дисперсия входных данных
* `rvariance`: остаточная дисперсия, которая представляет собой дисперсию между значениями входных данных, приближенных (по сегментам 2 строки).
* `line_fit`: числовой массив, держащий ряд значений наиболее приспособленной линии. Длина ряда равна длине входного массива. Он главным образом используется для построения диаграмм.
* `right_rsquare`: r-квадрат линии на правой стороне сплита, см. [series_fit_line()](series-fit-linefunction.md)
* `right_slope`: наклон правой приближенной линии (это от йакса)
* `right_interception`: перехват приблизительной левой линии (это b от y'ax'b)
* `right_variance`: дисперсия входных данных на правой стороне раскола
* `right_rvariance`: остаточная дисперсия входных данных на правой стороне раскола
* `left_rsquare`: r-квадрат линии на левой стороне сплита, см. [series_fit_line()](series-fit-linefunction.md)
* `left_slope`: наклон левой приближенной линии (это от йакса)
* `left_interception`: перехват приблизительной левой линии (это b от y'ax'b)
* `left_variance`: отклонение входных данных на левой стороне раскола
* `left_rvariance`: остаточная дисперсия входных данных на левой стороне раскола

*Обратите внимание,* что эта функция возвращает несколько столбцов, поэтому она не может быть использована в качестве аргумента для другой функции.

**Синтаксис**

проект `series_fit_2lines(` *x*`)`
* Вернут все упомянутые выше столбцы со следующими названиями: series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx и т.д.
проект (rs, si,`series_fit_2lines(`*x* v)`)`
* Вернутся следующие столбцы: rs (r-square), si (индекс разделения), v (отклонение), а остальные будут выглядеть как series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit и т.д. расширяются (rs, si, v)`series_fit_2lines(`*x*`)`
* Возвращает только: rs (R-квадрат), si (индекс разбиения) и v (дисперсия).
  
**Аргументы**

* *x*: Динамический массив числовых значений.  

> [!TIP]
> Наиболее удобным способом использования этой функции является ее применение к результатам оператора [make-series.](make-seriesoperator.md)

**Примеры**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="Серия подходит 2 линии":::
