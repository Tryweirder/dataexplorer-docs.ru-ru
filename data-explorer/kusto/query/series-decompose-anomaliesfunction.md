---
title: series_decompose_anomalies () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны series_decompose_anomalies() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 7d9ca0585b40a97d21690f908a50de5be493c412
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663632"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

Обнаружение аномалий на основе разложения серии (относится к [series_decompose()](series-decomposefunction.md)) 

Принимает выражение, содержащее ряд (динамический числовой массив) в качестве ввода и извлекать аномальные точки с баллами.

**Синтаксис**

`series_decompose_anomalies (`*Серия* `[, ` *Порог* `,` *Сезонность* `,` *Тенденция* `, ` *Test_points* `, ` *AD_method* `,` *Seasonality_threshold*`])`

**Аргументы**

* *Серия*: Динамическая ячейка массива, которая представляет собой массив численных значений, как правило, в результате выход [из серии или](make-seriesoperator.md) [make_list](makelist-aggfunction.md) операторов
* *Порог*: Порог аномалии, по умолчанию 1.5 (k значение) для обнаружения легких или сильных аномалий
* *Сезонность*: несколько контролирующих сезонный анализ, содержащий либо
    * -1: автоматическое обнаружение сезонности (с помощью [series_periods_detect)](series-periods-detectfunction.md)(по умолчанию) 
    * 0: нет сезонности (т.е. пропустить извлечение этого компонента)
    * период: положительный целый ряд, с указанием ожидаемого периода в количестве единиц ыи. Например, если серия находится в 1h бункеров, еженедельный период составляет 168 бункеров
* *Тренд*: Строка, контролируя анализ тренда, содержащая либо    
    * "avg": определить компонент тренда как среднее значение серии (по умолчанию)
    * "нет": нет тренда, пропустить извлечение этого компонента 
    * "linefit": экстракт трендового компонента с использованием линейной регрессии
* *Test_points*: 0 «по умолчанию» или положительный целый ряд, указывая количество очков в конце серии, чтобы исключить из процесса обучения (регрессии). Этот параметр должен быть установлен для целей прогнозирования
* *AD_method*: Строка, контролируя метод обнаружения аномалий (см. [series_outliers)](series-outliersfunction.md)на остаточной временной серии, содержащей либо    
    * "ctukey": [тест на забор Туки](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) с пользовательским 10-90-м пернитие (по умолчанию)
    * "tukey": [тест на забор Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) со стандартным 25-75-м процентилями диапазона
* *Seasonality_threshold*: Порог для оценки сезонности, когда *сезонность* установлена `0.6` для автоматического обнаружения, порог оценки по умолчанию (для более подробной информации см.: [series_periods_detect)](series-periods-detectfunction.md)


**Вернуться**

 Функция возвращает следующие соответствующие серии:

* `ad_flag`: тернари серия, содержащая (No 1, -1, 0) разметка вверх/вниз/нет аномалии соответственно
* `ad_score`: оценка аномалии
* `baseline`: прогнозируемое значение серии в соответствии с разложением

**Подробнее об алгоритме**

Эта функция следует следующим шагам:
1. Вызовы [series_decompose ()](series-decomposefunction.md) с соответствующими параметрами для создания базовой серии и остаточной серии
2. Вычисляет ad_score серии, применяя [series_outliers()](series-outliersfunction.md) с выбранным методом обнаружения аномалий в серии остатков
3. Вычисляет серию ad_flag, применяя порог на ad_score, чтобы разметить вверх/вниз/нет аномалии соответственно
 
**Примеры**

**1. Выявление аномалий в еженедельной сезонности**

В следующем примере мы создаем серию с еженедельной сезонностью, затем добавляем к ней некоторые выбросы. `series_decompose_anomalies`автообнаруживает сезонность и генерирует базовый условную, которая фиксирует повторяющийся шаблон. Выбросы, которые мы добавили, могут быть четко замечены в компоненте ad_score.

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

:::image type="content" source="images/samples/series-decompose-anomalies1.png" alt-text="Серия разлагает аномалии 1":::

**2. Выявление аномалий в еженедельной сезонности с тенденцией**

В этом примере мы добавляем тренд в серию из предыдущего примера. Во-первых, `series_decompose_anomalies` мы запускаем с параметрами по умолчанию, в которых значение тренда `avg` по умолчанию занимает только среднее значение и не вычисляет тренд, мы видим, что генерируемый базовый узел не содержит тренда и менее точен по сравнению с предыдущим примером, следовательно, некоторые выбросы, которые мы вставили в данные, не обнаруживаются из-за более высокой дисперсии.

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
:::image type="content" source="images/samples/series-decompose-anomalies2.png" alt-text="Серия разлагает аномалии 2":::

Далее мы запускаем тот же пример, но так как `linefit` мы ожидаем тренд в серии, мы указываем в параметр тренда. Мы видим, что базовый уклад гораздо ближе к входиной серии. Все вставленные мы выбросы обнаружены, а также некоторые ложные срабатывания (см. следующий пример по настройке порога).

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

:::image type="content" source="images/samples/series-decompose-anomalies3.png" alt-text="Серия разлагает аномалии 3":::

**3. Настройка порога обнаружения аномалий**

В предыдущем примере несколько шумных точек были обнаружены в качестве аномалий, в этом примере мы увеличиваем порог обнаружения аномалий с 1,5 до 2,5 межпроцентного диапазона, так что обнаруживаются только более сильные аномалии. Мы видим, что теперь обнаруживаются только выбросы, которые мы вставили в данные.

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

:::image type="content" source="images/samples/series-decompose-anomalies4.png" alt-text="Серия разлагает аномалии 4":::
