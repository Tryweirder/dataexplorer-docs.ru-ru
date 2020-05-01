---
title: series_decompose_forecast () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается series_decompose_forecast () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 97f87a7390ab099886e84642b2eb46a8087b6da9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618855"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Прогноз на основе декомпозиции ряда.

Принимает выражение, содержащее ряд (динамический числовой массив) в качестве входных данных, и прогнозирует значения последних конечных точек (см. [series_decompose](series-decomposefunction.md) для получения дополнительных сведений о методе unкомпоновки).
 
**Синтаксис**

`series_decompose_forecast(`*Series* `,` *Точки* `,` *Seasonality_threshold* *Trend* *Seasonality* ряда сезонности`,` тренд Seasonality_threshold `[,``])`

**Аргументы**

* *Ряд*: ячейка динамического массива, которая представляет собой массив числовых значений. Обычно результат выполнения операторов [make-Series](make-seriesoperator.md) или [make_list](makelist-aggfunction.md) .
* *Points*: целое число, указывающее количество точек в конце ряда для прогнозирования (прогноз). Эти точки исключаются из процесса обучения (регрессия).
* *Сезонности*: целое число, контролирующее сезонный анализ, содержащее один из следующих элементов:
    * -1: автоматическое определение сезонности с помощью [series_periods_detect](series-periods-detectfunction.md) (по умолчанию). 
    * period: положительное целое число, определяющее ожидаемый период в количестве ячеек. Например, если ряд находится в ячейках 1 ч, еженедельный период составляет 168 ячеек.
    * 0: нет сезонности (пропустить извлечение этого компонента).   
* *Тенденция*. строка, управляющая анализом тенденций, содержащая один из следующих элементов:
    * "линефит": извлечение компонента тренда с использованием линейной регрессии (по умолчанию).    
    * "AVG": определение компонента тренда как среднего (x).
    * "None": нет тренда, пропустите извлечение этого компонента.   
* *Seasonality_threshold*: пороговое значение для оценки сезонности, если для *сезонности* задано автоматическое обнаружение, пороговое `0.6`значение оценки по умолчанию —. Дополнительные сведения см. в разделе [series_periods_detect](series-periods-detectfunction.md).

**Вернул**

 Динамический массив с прогнозной серией
  

**Примечания**

* Динамический массив исходных входных рядов должен включать в себя слоты числовых *точек* для прогнозирования. обычно это делается с помощью [серии make](make-seriesoperator.md) и указывает время окончания в диапазоне, включающее временные рамки для прогнозирования.
    
* Необходимо включить сезонности и (или) тенденцию, в противном случае функция является избыточной и возвращает ряд, заполненный нулями.

**Пример**

В следующем примере мы создаем серию из 4 недель в почасовом Гране с еженедельным сезонностиом и небольшой последующей тенденцией, `make-series` затем используем и добавляем еще одну пустую неделю к ряду. `series_decompose_forecast`вызывается с неделей (24 * 7 точек), она автоматически обнаруживает сезонности и тенденцию и создает прогноз на протяжении всего 5 недель. 

```kusto
let ts=range t from 1 to 24*7*4 step 1 // generate 4 weeks of hourly data
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| make-series y=max(y) on Timestamp in range(datetime(2018-03-01 05:00), datetime(2018-03-01 05:00)+24*7*5h, 1h); // create a time series of 5 weeks (last week is empty)
ts 
| extend y_forcasted = series_decompose_forecast(y, 24*7)  // forecast a week forward
| render timechart 
```

:::image type="content" source="images/series-decompose-forecastfunction/series-decompose-forecast.png" alt-text="Прогноз разбиения ряда":::
