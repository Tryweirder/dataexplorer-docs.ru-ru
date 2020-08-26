---
title: Обнаружение аномалий временных рядов & прогнозирование в Azure обозреватель данных
description: Узнайте, как анализировать данные временных рядов для обнаружения аномалий и прогнозирования с помощью Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/24/2019
ms.openlocfilehash: 398d2a4c72d1ebb0fcc4961987402fb953cb5608
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872545"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Обнаружение аномалий и прогнозирование в Azure обозреватель данных

Azure обозреватель данных выполняет сбор данных телеметрии из облачных служб или устройств IoT. Эти данные анализируются для получения различных ценных сведений, таких как мониторинг работоспособности служб, физические производственные процессы, тенденции использования и прогноз нагрузки. Анализ выполняется по временным рядам выбранных метрик, чтобы нахождение шаблона отклонения метрики относительно обычного обычного шаблона базовых показателей. Azure обозреватель данных содержит собственную поддержку для создания, обработки и анализа нескольких временных рядов. Он может создавать и анализировать тысячи временных рядов за считаные секунды, позволяя практически в реальном времени отслеживать решения и рабочие процессы.

В этой статье подробно описаны возможности обнаружения аномалий временных рядов Azure обозреватель данных и прогнозирования. Применимые функции временных рядов основаны на надежной хорошо известной модели декомпозиции, где каждый исходный временный ряд разбивается на сезонные, трендовые и остаточные компоненты. Аномалии обнаруживаются выбросами по остаточному компоненту, а прогнозирование выполняется путем экстраполяции сезонных и трендов компонентов. Реализация обозреватель данных Azure значительно расширяет базовую модель декомпозиции путем автоматического обнаружения сезонности, надежного анализа выбросов и векторной реализации для обработки тысяч временных рядов за считаные секунды.

## <a name="prerequisites"></a>Предварительные условия

Прочтите [анализ временных рядов в обозреватель данных Azure](time-series-analysis.md) , чтобы получить общие сведения о возможностях временных рядов.

## <a name="time-series-decomposition-model"></a>Модель декомпозиции временных рядов

Реализация Azure обозреватель данных Native для прогнозирования временных рядов и обнаружения аномалий использует хорошо известную модель декомпозиции. Эта модель применяется к временным рядам метрик, ожидаемым для периодической проверки манифеста и поведения тенденций, таких как трафик службы, пульсы компонентов и периодические измерения Интернета вещей для прогнозирования будущих значений метрик и выявления аномальных. Предполагается, что этот процесс регрессии отличается от ранее известных сезонов и тенденций, а временные ряды распределяются случайным образом. Затем можно прогнозировать будущие значения метрик из компонентов сезонного и тенденция, совокупно именуемые базовые показатели и игнорировать остаточную часть. Вы также можете обнаружить аномальные значения на основе анализа выбросов, используя только остаточную часть.
Чтобы создать модель декомпозиции, используйте функцию [`series_decompose()`](kusto/query/series-decomposefunction.md) . `series_decompose()`Функция принимает ряд временных рядов и автоматически разделяет каждый временный ряд на его сезонные, трендовые, остаточные и базовые компоненты. 

Например, можно разбить трафик внутренней веб-службы с помощью следующего запроса:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

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

* Исходный временный ряд имеет метку **num** (красный цвет). 
* Процесс начинается с автоматического определения сезонности с помощью функции [`series_periods_detect()`](kusto/query/series-periods-detectfunction.md) и извлекает **сезонный** шаблон (в сиреневый цвет).
* Сезонный шаблон вычитается из исходного временного ряда, а линейная регрессия выполняется с помощью функции [`series_fit_line()`](kusto/query/series-fit-linefunction.md) для поиска компонента **тренда** (светло-голубой).
* Функция вычитает тенденцию, а остаток — **остаточный** компонент (зеленый).
* Наконец, функция добавляет компоненты сезонного и тренда для создания **базовых показателей** (синим цветом).

## <a name="time-series-anomaly-detection"></a>Обнаружение аномалий во временных рядах

Функция [`series_decompose_anomalies()`](kusto/query/series-decompose-anomaliesfunction.md) находит аномальные моменты на наборе временных рядов. Эта функция вызывает `series_decompose()` для создания модели декомпозиции, а затем выполняется [`series_outliers()`](kusto/query/series-outliersfunction.md) в компоненте остаточной компоновки. `series_outliers()` Вычисляет оценки аномалий для каждой точки остаточного компонента с помощью теста ограждения Tukey. Оценки аномалий свыше 1,5 или ниже-1,5 указывают на умеренную аномалию или отклонять соответственно. Оценки аномалий свыше 3,0 или ниже-3,0 указывают на серьезную аномалию. 

Следующий запрос позволяет обнаруживать аномалии во внутреннем трафике веб-службы:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

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

* Исходный временный ряд (красный цвет). 
* Базовый компонент (сезон + тенденция) (синий).
* Аномальные точки (фиолетовые) поверх исходного временного ряда. Аномальные точки значительно отличаются от ожидаемых базовых значений.

## <a name="time-series-forecasting"></a>Прогнозирование временных рядов

Функция [`series_decompose_forecast()`](kusto/query/series-decompose-forecastfunction.md) прогнозирует будущие значения набора временных рядов. Эта функция вызывает `series_decompose()` для создания модели декомпозиции, а затем для каждого временного ряда выполняет экстраполяцию базового компонента в будущее.

Следующий запрос позволяет спрогнозировать трафик веб-службы на следующей неделе:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

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

* Исходная метрика (красным цветом). Будущие значения отсутствуют и по умолчанию имеют значение 0.
* Экстраполяция базового компонента (синим цветом) для прогнозирования значений на следующей неделе.

## <a name="scalability"></a>Масштабируемость

Синтаксис языка запросов Azure обозреватель данных позволяет выполнять один вызов для обработки нескольких временных рядов. Его уникальная оптимизированная реализация обеспечивает высокую производительность, что очень важно для эффективного обнаружения аномалий и прогнозирования при мониторинге тысяч счетчиков в сценариях практически в реальном времени.

В следующем запросе показана Одновременная обработка трех временных рядов:

**\[**[**Щелкните, чтобы запустить запрос**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

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

## <a name="summary"></a>Итоги

В этом документе описаны собственные функции Azure обозреватель данных для обнаружения аномалий временных рядов и прогнозирования. Каждый исходный временный ряд разбивается на сезонные, тенденции и остаточные компоненты для выявления аномалий и (или) прогнозирования. Эти функции можно использовать для сценариев мониторинга практически в реальном времени, таких как обнаружение сбоев, диагностическое обслуживание, прогнозирование спроса и нагрузки.

## <a name="next-steps"></a>Дальнейшие шаги

Узнайте о [возможностях машинного обучения](machine-learning-clustering.md) в Azure обозреватель данных.
