---
title: series_outliers() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_outliers () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 47e479ce7fe09b2456405ac3f7daed4721374f32
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663462"
---
# <a name="series_outliers"></a>series_outliers()

Оценки аномалий в серии.

Принимает выражение, содержащее динамический числовой массив в качестве ввода, и генерирует динамический численный массив одинаковой длины. Каждое значение массива указывает на оценку возможной аномалии с помощью [теста Tukey.](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test) Значение больше 1,5 или меньше –1,5 означает повышение или снижение аномалии (соответственно) в одном элементе входных данных.   

**Синтаксис**

`series_outliers(`*x*`, `*вид*`, `*ignore_val*`, `*min_percentile*`, `*max_percentile*`)`

**Аргументы**

* *x*: Динамическая ячейка массива, представляющая собой массив числовых значений
* *вид*: Алгоритм обнаружения выбросов. В `"tukey"` настоящее время поддерживает `"ctukey"` (традиционный Tukey) и (обычай Tukey). Значение по умолчанию — `"ctukey"`.
* *ignore_val:* числовое значение, указывающее недостающие значения в серии, значение по умолчанию является двойным (нулевым). Оценка нулевки и игнорировать `0`значения установлен на .
* *min_percentile*: для каляции нормального диапазона между квантилями, по умолчанию 10, пользовательские значения поддерживаются в диапазоне `[2.0, 98.0]` (только)`ctukey` 
* *max_percentile*: то же самое, по умолчанию `[2.0, 98.0]` 90, пользовательские значения поддерживается находятся в диапазоне (ctukey только) 

В следующей таблице `"tukey"` `"ctukey"`описаны различия между:

| Алгоритм | Диапазон квантилей по умолчанию | Поддержка пользовательского диапазона квантилей |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25 %–75 %              | нет                             |
| `"ctukey"`| 10 %–90 %              | Да                            |


> [!TIP]
> Наиболее удобным способом использования этой функции является ее применение к результатам оператора [make-series.](make-seriesoperator.md)

**Пример**

Предположим, у вас есть временные ряды с некоторым шумом, который создает выбросы, и вы хотели бы заменить эти выбросы (шум) со средним значением, вы можете использовать series_outliers() для обнаружения выбросов, а затем заменить их:

```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" border="false" source="images/samples/series-outliers.png" alt-text="Выбросы серии":::
