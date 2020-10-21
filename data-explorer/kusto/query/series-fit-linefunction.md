---
title: series_fit_line () — обозреватель данных Azure
description: В этой статье описывается series_fit_line () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9731e3384fb0109c37ad6c0ca262a954ef5dd470
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248455"
---
# <a name="series_fit_line"></a>series_fit_line()

Применяет линейную регрессию к ряду, возвращая несколько столбцов.  

Принимает выражение, содержащее динамический числовой массив, в качестве входных данных и выполняет [линейную регрессию](https://en.wikipedia.org/wiki/Line_fitting) для поиска строки, которая лучше подходит для нее. Эту функцию следует использовать для массивов временных рядов, чтобы подбирать выходные данные оператора make-series. Функция создает следующие столбцы:
* `rsquare`: [r-Square](https://en.wikipedia.org/wiki/Coefficient_of_determination) — стандартная мера качества подгонки. Значение в диапазоне [0-1], где 1 — наилучшее соответствие, а 0 означает, что данные не упорядочены и не соответствуют ни одной строке. 
* `slope`: Наклон приближенной строки ("a" от y = ax + b).
* `variance`: Дисперсия входных данных.
* `rvariance`: Остаточная дисперсия, которая является дисперсией между значениями входных данных приблизительного значения.
* `interception`: Перехват приближенной строки ("b" от y = ax + b).
* `line_fit`: Числовой массив, содержащий ряд значений наиболее подходящей строки. Длина ряда равна длине входного массива. Значение, используемое для построения диаграмм.

## <a name="syntax"></a>Синтаксис

`series_fit_line(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: динамический массив числовых значений.

> [!TIP]
> Самый удобный способ использования этой функции — применить его к результатам оператора [make-Series](make-seriesoperator.md) .

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="Линия подгонки ряда":::

| RSquare | Кривизна | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0,982   | 2,730 | 98,628   | 1,686     | -1,666       | 1,064, 3,7945, 6,526, 9,256, 11,987, 14,718, 17,449, 20,180, 22,910, 25,641, 28,371, 31,102 |
