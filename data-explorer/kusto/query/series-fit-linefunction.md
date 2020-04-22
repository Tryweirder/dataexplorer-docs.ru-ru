---
title: series_fit_line () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны series_fit_line () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9b36dc51dded4de7e5b57145b918e6741ab8757d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663482"
---
# <a name="series_fit_line"></a>series_fit_line()

Применяет линейную регрессию в серии, возвращая несколько столбцов.  

Принимает выражение, содержащее динамический числовой массив в качестве ввода, и выполняет [линейную регрессию,](https://en.wikipedia.org/wiki/Line_fitting) чтобы найти линию, которая лучше всего подходит. Эту функцию следует использовать для массивов временных рядов, чтобы подбирать выходные данные оператора make-series. Он генерирует следующие столбцы:
* `rsquare`: [r-квадрат](https://en.wikipedia.org/wiki/Coefficient_of_determination) является стандартной мерой качества подгонки. Это число в диапазоне [0–1], где 1 означает максимальное соответствие, а 0 означает, что данные абсолютно не упорядочены и не соответствуют какой-либо линии. 
* `slope`: наклон приблизительной линии (это от y'ax'b)
* `variance`: дисперсия входных данных
* `rvariance`: остаточная дисперсия, которая представляет собой дисперсию между значениями входных данных, приближенными.
* `interception`: перехват приблизительной линии (это b от y'ax'b)
* `line_fit`: числовой массив, держащий ряд значений наиболее приспособленной линии. Длина ряда равна длине входного массива. Он главным образом используется для построения диаграмм.

**Синтаксис**

`series_fit_line(`*X*`)`

**Аргументы**

* *x*: Динамический массив числовых значений.

> [!TIP]
> Наиболее удобным способом использования этой функции является ее применение к результатам оператора [make-series.](make-seriesoperator.md)

**Примеры**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/samples/series-fit-line.png" alt-text="Линия подгонки серии":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0,982   | 2,730 | 98,628   | 1,686     | -1,666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |