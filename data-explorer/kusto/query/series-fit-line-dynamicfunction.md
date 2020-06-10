---
title: series_fit_line_dynamic () — обозреватель данных Azure
description: В этой статье описывается series_fit_line_dynamic () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f3fb8361cfb281ad39dee7a15a690c2b94c79bea
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664931"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

Применяет линейную регрессию к ряду, возвращая динамический объект.  

Принимает выражение, содержащее динамический числовой массив в качестве входных данных, и выполняет [линейную регрессию](https://en.wikipedia.org/wiki/Line_fitting) для поиска строки, которая лучше подходит для нее. Эту функцию следует использовать для массивов временных рядов, чтобы подбирать выходные данные оператора make-series. Он создает динамическое значение со следующим содержимым:
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) — стандартная мера качества подгонки. Это число в диапазоне [0-1], где 1 — наилучшее соответствие, а 0 означает, что данные не упорядочены и не соответствуют ни одной строке.
* `slope`: Наклон приближенной строки ( *a*-значение от *y = ax + b*)
* `variance`: Дисперсия входных данных
* `rvariance`: Остаточная дисперсия, которая является дисперсией между значениями входных данных и приблизительными элементами.
* `interception`: Перехват приближенной строки ( *b*-значение из *y = ax + b*)
* `line_fit`: Числовой массив, содержащий ряд значений строки наилучшего соответствия. Длина ряда равна длине входного массива. Он используется главным образом для построения диаграмм.

Этот оператор аналогичен [series_fit_line](series-fit-linefunction.md), но в отличие от `series-fit-line` него возвращает динамический контейнер.

**Синтаксис**

`series_fit_line_dynamic(`*x*`)`

**Аргументы**

* *x*: динамический массив числовых значений.

> [!TIP]
> Наиболее удобный способ использования этой функции — применение его к результатам оператора [make-Series](make-seriesoperator.md) .

**Примеры**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```
 
:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="Линия подгонки ряда":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0,982   | 2,730 | 98,628   | 1,686     | -1,666       | 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, 31,102 |
 
