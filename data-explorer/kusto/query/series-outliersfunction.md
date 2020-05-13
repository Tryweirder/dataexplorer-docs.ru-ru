---
title: series_outliers () — обозреватель данных Azure
description: В этой статье описывается series_outliers () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 864638f8e03487a35eefa83fa3951d2ecefc27c7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372543"
---
# <a name="series_outliers"></a>series_outliers()

Оценки аномалий в ряде.

Принимает выражение, содержащее динамический числовой массив, в качестве входных данных и создает динамический числовой массив с такой же длиной. Каждое значение массива указывает оценку возможной аномалии с помощью [теста Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test). Значение больше 1,5 или меньше –1,5 означает повышение или снижение аномалии (соответственно) в одном элементе входных данных.   

**Синтаксис**

`series_outliers(`*x* `, ` *тип* `, ` *ignore_val* `, ` *min_percentile* `, ` *max_percentile*`)`

**Аргументы**

* *x*: ячейка динамического массива, представляющая собой массив числовых значений
* *тип*: алгоритм обнаружения выбросов. В настоящее время поддерживает `"tukey"` (традиционные Tukey) и `"ctukey"` (Custom Tukey). Значение по умолчанию — `"ctukey"`.
* *ignore_val*: числовое значение, указывающее на отсутствие значений в ряде, по умолчанию — Double (null). Оценка значений NULL и ignore имеет значение `0` .
* *min_percentile*: для межквартильного обычного диапазона квантилей, по умолчанию — 10, поддерживаемые пользовательские значения находятся в диапазоне `[2.0, 98.0]` ( `ctukey` только). 
* *max_percentile*: то же, значение по умолчанию — 90, поддерживаемые пользовательские значения находятся в диапазоне `[2.0, 98.0]` (только ctukey) 

В следующей таблице описаны различия между `"tukey"` и `"ctukey"` .

| Алгоритм | Диапазон квантилей по умолчанию | Поддержка пользовательского диапазона квантилей |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25 %–75 %              | нет                             |
| `"ctukey"`| 10 %–90 %              | Да                            |


> [!TIP]
> Наиболее удобный способ использования этой функции — применение ее к результатам оператора [make-Series](make-seriesoperator.md) .

**Пример**

Предположим, что у вас есть временные ряды, которые создают выбросы и вы хотите заменить эти выбросы (шум) средним значением, можно использовать series_outliers () для обнаружения выбросов и их замены.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" source="images/series-outliersfunction/series-outliers.png" alt-text="Выбросы ряда" border="false":::
