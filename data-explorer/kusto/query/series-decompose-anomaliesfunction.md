---
title: series_decompose_anomalies () — обозреватель данных Azure
description: В этой статье описывается series_decompose_anomalies () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: ded1f7ed499d0a8379fdf5b8e9949fa06351fc07
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250189"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

Обнаружение аномалий основано на декомпозиции рядов.
Дополнительные сведения см. в разделе [series_decompose ()](series-decomposefunction.md).

Функция принимает выражение, содержащее ряд (динамический числовой массив) в качестве входных данных, и извлекает аномальные точки с результатами.

## <a name="syntax"></a>Синтаксис

`series_decompose_anomalies (`*Ряд* `[, ` *Пороговое значение* `,` *Сезонности* `,` *Тенденция* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>Аргументы

* *Ряд*: ячейка динамического массива, которая представляет собой массив числовых значений, обычно результирующий результат операторов [make-Series](make-seriesoperator.md) или [make_list](makelist-aggfunction.md) .
* *Пороговое*значение: пороговое значение аномалий, значение по умолчанию 1,5 (k) для обнаружения умеренных или более надежных аномалий
* *Сезонности*: целое число, контролирующее сезонный анализ, содержащий либо
    * -1: автоматическое определение сезонности (с использованием [series_periods_detect](series-periods-detectfunction.md)) [по умолчанию]
    * 0: нет сезонности (то есть пропустить извлечение этого компонента)
    * period: положительное целое число, задающее ожидаемый период в единицах измерения количества ячеек. Например, если ряд находится в одном часовом интервале, то еженедельный период составляет 168 ячеек.
* *Тенденция*. строка, контролирующая анализ тенденций, содержащий либо
    * "AVG": определение компонента тренда в качестве среднего значения для ряда [по умолчанию]
    * "None": нет тренда, пропуск извлечения этого компонента
    * "линефит": извлечение компонента тренда с использованием линейной регрессии
* *Test_points*: 0 [default] или положительное целое число, указывающее количество точек в конце последовательности, исключаемых из процесса обучения (регрессия). Этот параметр должен быть задан для целей прогнозирования.
* *AD_method*: строка, управляющая методом обнаружения аномалий в остаточном ряде времени, содержащая одно из следующих значений:
    * "ctukey": [тест ограждения Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) с настраиваемым диапазоном из 10-90 процентиль [по умолчанию]
    * "tukey": [тест ограждения tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) с диапазоном "Стандартный 25-75 процентиль" Дополнительные сведения о остаточном временном ряде см. в разделе [series_outliers](series-outliersfunction.md)
* *Seasonality_threshold*: пороговое значение для показателя сезонности, если для *сезонности* задано автоматическое обнаружение. Порог оценки по умолчанию — `0.6` . Дополнительные сведения см. в разделе [series_periods_detect](series-periods-detectfunction.md)

## <a name="returns"></a>Результаты

 Функция возвращает следующие соответствующие ряды:

* `ad_flag`: Ряд ternary, содержащий (+ 1,-1, 0), помечая соответственно вверх/вниз/без аномалий
* `ad_score`: Оценка аномалий
* `baseline`: Прогнозируемое значение ряда в соответствии с разкомпозицией

## <a name="the-algorithm"></a>Алгоритм

Эта функция выполняет следующие действия:
1. Вызывает [series_decompose ()](series-decomposefunction.md) с соответствующими параметрами для создания ряда базовых и остаточных значений.
1. Вычисляет ad_score ряд, применяя [series_outliers ()](series-outliersfunction.md) с выбранным методом обнаружения аномалий в рядах остатков.
1. Вычисляет ad_flag ряд, применяя пороговое значение на ad_score, чтобы отметить и отмечать их соответственно.
 
## <a name="examples"></a>Примеры

### <a name="detect-anomalies-in-weekly-seasonality"></a>Обнаружение аномалий в еженедельном сезонности

В следующем примере создайте серию с еженедельным сезонности, а затем добавьте в нее некоторые выбросы. `series_decompose_anomalies` автоматически обнаруживает сезонности и формирует базовый уровень, который фиксирует Повторяющийся шаблон. Добавленные выбросы можно четко определить в компоненте ad_score.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers.png" alt-text="Еженедельный сезонности с отображением базовых показателей и выбросов" border="false":::

### <a name="detect-anomalies-in-weekly-seasonality-with-trend"></a>Обнаружение аномалий в еженедельном сезонностие с тенденциями

В этом примере добавьте тенденцию к ряду из предыдущего примера. Сначала запустите `series_decompose_anomalies` с параметрами по умолчанию, в которых значение тренда `avg` по умолчанию принимает только среднее и не вычисляет тренд. Созданный базовый план не содержит тенденцию и меньше точного по сравнению с предыдущим примером. Следовательно, некоторые выбросы, вставленные в данные, не обнаруживаются из-за более высокой дисперсии.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="Еженедельный сезонности с отображением базовых показателей и выбросов" border="false":::

Затем выполните тот же пример, но так как вы ожидаете тренд в ряде, укажите `linefit` в параметре тенденция. Вы видите, что базовый план намного ближе к входному ряду. Обнаруживаются все вставленные выбросы, а также некоторые ложные срабатывания. См. Следующий пример, посвященный настройке порогового значения.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 1.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="Еженедельный сезонности с отображением базовых показателей и выбросов" border="false":::

### <a name="tweak-the-anomaly-detection-threshold"></a>Настройка порогового значения обнаружения аномалий

В предыдущем примере в качестве аномалий обнаружено несколько бесшумных точек. Теперь Увеличьте пороговое значение обнаружения аномалий по умолчанию с 1,5 по 2,5. Используйте этот диапазон интерперцентиле, чтобы были обнаружены только более надежные аномалии. Теперь будут обнаружены только выбросы, вставленные в данные.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and onlgoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 2.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="Еженедельный сезонности с отображением базовых показателей и выбросов" border="false":::
