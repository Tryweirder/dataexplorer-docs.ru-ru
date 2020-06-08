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
ms.openlocfilehash: 80e20e70bc51045f68fd3ef2068f099d750b2b3f
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512441"
---
# <a name="series_outliers"></a>series_outliers()

Оценки аномалий в ряде.

Функция принимает выражение с динамическим числовым массивом в качестве входных данных и создает динамический числовой массив с такой же длиной. Каждое значение массива указывает оценку возможной аномалии с помощью ["Tukey Test"](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test). Значение, превышающее 1,5 в одном элементе входных данных, указывает на аномалию подъема или отклонения. Значение меньше-1,5 указывает на аномалию отклонения.

**Синтаксис**

`series_outliers(`*x* `, ` *тип* `, ` *ignore_val* `, ` *min_percentile* `, ` *max_percentile*`)`

**Аргументы**

* *x*: ячейка динамического массива, которая является массивом числовых значений
* *тип*: алгоритм обнаружения выбросов. В настоящее время поддерживает `"tukey"` (традиционный "Tukey") и `"ctukey"` (Custom "Tukey"). Значение по умолчанию — `"ctukey"`.
* *ignore_val*: числовое значение, указывающее на отсутствие значений в ряде. Значение по умолчанию — Double (null). Оценка значений NULL и ignore имеет значение`0`
* *min_percentile*: для вычисления обычного диапазона квантилей. По умолчанию — 10, поддерживаемые пользовательские значения находятся в диапазоне `[2.0, 98.0]` ( `ctukey` только).
* *max_percentile*: то же, значение по умолчанию — 90, поддерживаемые пользовательские значения находятся в диапазоне `[2.0, 98.0]` (только ctukey)

В следующей таблице описаны различия между `"tukey"` и `"ctukey"` .

| Алгоритм | Диапазон квантилей по умолчанию | Поддержка пользовательского диапазона квантилей |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25 %–75 %              | Нет                             |
| `"ctukey"`| 10 %–90 %              | Да                            |

> [!TIP]
> Лучший способ использовать эту функцию — применить ее к результатам оператора [make-Series](make-seriesoperator.md) .

**Пример**

Временные ряды с некоторыми шумами создают выбросы. Если вы хотите заменить эти выбросы (шум) средним значением, используйте series_outliers () для обнаружения выбросов, а затем замените их.

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
