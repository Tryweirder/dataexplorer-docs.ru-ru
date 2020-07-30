---
title: series_decompose_forecast () — обозреватель данных Azure
description: В этой статье описывается series_decompose_forecast () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 21a57e4c49e982fbb113917abe173f89426c74ed
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345153"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Прогноз на основе декомпозиции ряда.

Принимает выражение, содержащее ряд (динамический числовой массив) в качестве входных данных, и прогнозирует значения последних конечных точек. Дополнительные сведения см. в разделе [series_decompose](series-decomposefunction.md).
 
## <a name="syntax"></a>Синтаксис

`series_decompose_forecast(`*Ряд* `,` *Точки* `[,` *Сезонности* `,` *Тенденция* `,` *Seasonality_threshold*`])`

## <a name="arguments"></a>Аргументы

* *Ряд*: ячейка динамического массива числовых значений. Как правило, результирующий результат операторов [make-Series](make-seriesoperator.md) или [make_list](makelist-aggfunction.md) .
* *Points*: целое число, указывающее количество точек в конце ряда для прогнозирования (прогноз). Эти точки исключаются из процесса обучения (регрессия).
* *Сезонности*: целое число, контролирующее сезонный анализ, содержащее одно из следующих значений:
    * -1: автоматическое обнаружение сезонности с помощью [series_periods_detect](series-periods-detectfunction.md) (по умолчанию).
    * period: положительное целое число, определяющее ожидаемый период в количестве ячеек. Например, если ряд находится в ячейках 1 ч, еженедельный период составляет 168 ячеек.
    * 0: нет сезонности (пропустить извлечение этого компонента).
* *Тенденция*. строка, управляющая анализом тенденций, содержащая один из следующих элементов:
    * `linefit`: Извлечение компонента тренда с использованием линейной регрессии (по умолчанию).
    * `avg`: Определите компонент тренда как Average (x).
    * `none`: Нет тренда, пропустите извлечение этого компонента.
* *Seasonality_threshold*: пороговое значение для показателя сезонности, если для *сезонности* задано автоматическое обнаружение. Порог оценки по умолчанию — `0.6` . Дополнительные сведения см. в разделе [series_periods_detect](series-periods-detectfunction.md).

**Вернул**

 Динамический массив с прогнозируемой серией.

> [!NOTE]
> * Динамический массив исходного входного ряда должен включать в себя несколько слотов *точек* для прогнозирования. Прогноз обычно выполняется с помощью [серии make](make-seriesoperator.md) и указывает время окончания в диапазоне, который включает в себя временные рамки для прогнозирования.
> * Необходимо включить сезонности или тенденцию, в противном случае функция является избыточной и просто возвращает ряд, заполненный нулями.

## <a name="example"></a>Пример

В следующем примере мы создаем серию из четырех недель в почасовом Гране, где еженедельно сезонности и небольшая находящаяся тенденция. Затем мы используем `make-series` и добавляем еще одну пустую неделю к ряду. `series_decompose_forecast`метод вызывается с неделей (24 * 7 точек) и автоматически обнаруживает сезонности и тенденцию и формирует прогноз всего за пять недель.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
 
