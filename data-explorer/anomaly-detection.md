---
title: Обнаружение аномалий временных рядов & прогнозирование в Azure Data Explorer
description: Узнайте, как анализировать данные временных рядов для обнаружения аномалий и прогнозирования с помощью Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: eac1237e098b90f526e441ef89f0902f72abde53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496710"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Обнаружение аномалий и прогнозирование в Azure Data Explorer

Azure Data Explorer осуществляет втечение сбор телеметрических данных с облачных служб или устройств IoT. Эти данные анализируются для различных аналитических данных, таких как работоспособность служб мониторинга, физические производственные процессы, тенденции использования и прогноз нагрузки. Анализ выполняется по временной серии выбранных метрик, чтобы найти шаблон отклонения метрики по отношению к ее типичному обычному базовому шаблону. Azure Data Explorer содержит назаемную поддержку для создания, манипуляции и анализа нескольких временных рядов. Он может создавать и анализировать тысячи временных рядов в считанные секунды, позволяя практически в режиме реального времени контролировать решения и рабочие процессы.

В этой статье подробно описаны возможности обнаружения и прогнозирования аномалий в области обнаружения и прогнозирования временных рядов Azure Data Explorer. Соответствующие функции временных рядов основаны на надежной хорошо известной модели разложения, где каждая оригинальная серия времени разлагается на сезонные, трендовые и остаточные компоненты. Аномалии обнаруживаются выбросами на остаточном компоненте, в то время как прогнозирование осуществляется путем экстраполирования сезонных и трендовых компонентов. Реализация Azure Data Explorer значительно улучшает базовую модель разложения за счет автоматического обнаружения сезонности, надежного анализа выбросов и векторизованной реализации для обработки тысяч временных рядов в считанные секунды.

## <a name="prerequisites"></a>Предварительные требования

Прочитайте [анализ временных рядов в Azure Data Explorer](/azure/data-explorer/time-series-analysis) для обзора возможностей временных рядов.

## <a name="time-series-decomposition-model"></a>Модель разложения временных рядов

Наивная реализация Azure Data Explorer для прогнозирования и обнаружения аномалий временных рядов использует хорошо известную модель разложения. Эта модель применяется к временным рядам метрик, которые, как ожидается, будут проявлять периодическое и трендовое поведение, такие как трафик службы, биение сердца компонентов и периодические измерения IoT для прогнозирования будущих метричных значений и обнаружения аномальных значений. Предположение этого процесса регрессии состоит в том, что, кроме ранее известного сезонного и трендового поведения, временные ряды распределяются случайным образом. Затем можно прогнозировать будущие значения метрик и из сезонных и трендовых компонентов, совместно названных базовых, и игнорировать остаточную часть. Можно также обнаружить аномальные значения на основе анализа выбросов, используя только остаточную часть.
Для создания модели разложения [`series_decompose()`](kusto/query/series-decomposefunction.md)используйте функцию. `series_decompose()` Функция принимает набор временных рядов и автоматически разлагает каждый раз, когда серии к сезонным, трендовым, остаточным и базовым компонентам. 

Например, можно разложить трафик внутреннего веб-сервиса с помощью следующего запроса:

**\[**[**Нажмите, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Разбор временного ряда](media/anomaly-detection/series-decompose-timechart.png)

* Исходная серия времени помечена **num** (красным цветом). 
* Процесс начинается с автоматического обнаружения сезонности [`series_periods_detect()`](kusto/query/series-periods-detectfunction.md) с помощью функции и извлечения **сезонного** шаблона (в фиолетовом).
* Сезонная модель вычитается из исходных временных рядов, и линейная регрессия запущена с использованием функции [`series_fit_line()`](kusto/query/series-fit-linefunction.md) поиска компонента **тренда** (в светло-голубом цвете).
* Функция вычитает тренд, а остальная часть является **остаточным** компонентом (зеленым цветом).
* Наконец, функция добавляет сезонные и трендовые компоненты для генерации **базового (синим** цветом).

## <a name="time-series-anomaly-detection"></a>Обнаружение аномалий во временных рядах

Функция [`series_decompose_anomalies()`](kusto/query/series-decompose-anomaliesfunction.md) находит аномальные точки в наборе временных рядов. Эта функция `series_decompose()` требует создания модели разложения, а затем выполняется [`series_outliers()`](kusto/query/series-outliersfunction.md) на остаточном компоненте. `series_outliers()`вычисляет оценки аномалий для каждой точки остаточного компонента с помощью теста забора Tukey. Оценки аномалий выше 1,5 или ниже -1,5 указывают на умеренный рост или снижение аномалий соответственно. Оценки аномалий выше 3,0 или ниже -3,0 указывают на сильную аномалию. 

Следующий запрос позволяет обнаружить аномалии во внутреннем трафике веб-сервисов:

**\[**[**Нажмите, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Обнаружение аномалий во временных рядах](media/anomaly-detection/series-anomaly-detection.png)

* Оригинальная серия тайм-тсериала (красным цветом). 
* Базовый компонент (сезонный и трендовый) (синим цветом).
* Аномальные точки (в фиолетовом) в верхней части оригинальной временной серии. Аномальные точки значительно отклоняются от ожидаемых базовых значений.

## <a name="time-series-forecasting"></a>Прогнозирование временных рядов

Функция [`series_decompose_forecast()`](kusto/query/series-decompose-forecastfunction.md) предсказывает будущие значения набора временных рядов. Эта функция `series_decompose()` требует построить модель разложения, а затем, для каждой временной серии, экстраполирует базовый компонент в будущее.

Следующий запрос позволяет предсказать трафик веб-сервисов на следующей неделе:

**\[**[**Нажмите, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Прогнозирование временных рядов](media/anomaly-detection/series-forecasting.png)

* Исходная метрика (красным цветом). Будущие значения отсутствуют и установлены до 0 по умолчанию.
* Экстраполировать базовый компонент (синим цветом) для прогнозирования значений на следующей неделе.

## <a name="scalability"></a>Масштабируемость

Синтаксис языка обработки языков Azure Data Explorer позволяет одному вызову обрабатывать несколько временных рядов. Его уникальная оптимизированная реализация обеспечивает быструю производительность, что имеет решающее значение для эффективного обнаружения аномалий и прогнозирования при мониторинге тысяч счетчиков в сценариях в режиме реального времени.

Следующий запрос показывает обработку трех временных рядов одновременно:

**\[**[**Нажмите, чтобы выполнить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Масштабируемость временных рядов](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Сводка

В этом документе подробно описаны функции native Azure Data Explorer для обнаружения и прогнозирования аномалий временных рядов. Каждая оригинальная серия времени разлагается на сезонные, трендовые и остаточные компоненты для обнаружения аномалий и/или прогнозирования. Эти функции могут использоваться для сценариев мониторинга в режиме реального времени, таких как обнаружение неисправностей, прогностическое обслуживание, а также прогнозирование спроса и нагрузки.

## <a name="next-steps"></a>Следующие шаги

Узнайте о [возможностях машинного обучения](/azure/data-explorer/machine-learning-clustering) в Azure Data Explorer.
