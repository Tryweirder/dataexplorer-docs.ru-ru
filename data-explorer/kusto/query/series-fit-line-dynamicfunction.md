---
title: series_fit_line_dynamic () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается series_fit_line_dynamic () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 170348dd530b581f85e0323563be324d5b795511
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618723"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

Применяет линейную регрессию к ряду, возвращая динамический объект.  

Принимает выражение, содержащее динамический числовой массив в качестве входных данных, и выполняет [линейную регрессию](https://en.wikipedia.org/wiki/Line_fitting) , чтобы найти строку, которая лучше подходит для нее. Эту функцию следует использовать для массивов временных рядов, чтобы подбирать выходные данные оператора make-series. Он создает динамическое значение со следующим содержимым:
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) — стандартная мера качества подгонки. Это число в диапазоне [0–1], где 1 означает максимальное соответствие, а 0 означает, что данные абсолютно не упорядочены и не соответствуют какой-либо линии. 
* `slope`: наклон приближенной строки (это от y = ax + b)
* `variance`: дисперсия входных данных
* `rvariance`: Остаточная дисперсия, которая является дисперсией между значениями входных данных приблизительного значения.
* `interception`: перехват приблизительной строки (это б от y = ax + b)
* `line_fit`: числовой массив, содержащий ряд значений наиболее подходящей строки. Длина ряда равна длине входного массива. Он главным образом используется для построения диаграмм.

Этот оператор аналогичен [series_fit_line](series-fit-linefunction.md), но в отличие от `series-fit-line` него возвращает динамический контейнер.

**Синтаксис**

`series_fit_line_dynamic(`*x*`)`

**Аргументы**

* *x*: динамический массив числовых значений.

> [!TIP]
> Наиболее удобный способ использования этой функции — применение ее к результатам оператора [make-Series](make-seriesoperator.md) .

**Примеры**

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