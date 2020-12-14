---
title: Библиотека функций — Azure обозреватель данных
description: В этой статье описываются определяемые пользователем функции, которые расширяют возможности Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: b7e066133817184664e37aec52a562525afa9504
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388959"
---
# <a name="functions-library"></a>Библиотека функций

В приведенной ниже статье содержится список [определяемых пользователем функций типа UDF](../query/functions/user-defined-functions.md).

Код определяемых пользователем функций указан в статьях.  Он может использоваться в инструкции Let, внедренной в запрос, или может быть сохранена в базе данных с помощью [`.create function`](../management/create-function.md) .

## <a name="machine-learning-functions"></a>Функции машинного обучения

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|Кластеризации с использованием алгоритма k-средних. |
|[predict_fl()](predict-fl.md)|Прогнозирование с использованием существующей обученной модели машинного обучения. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Прогнозирование с использованием существующей обученной модели машинного обучения в формате ONNX. |

## <a name="promql-functions"></a>Функции Промкл

В следующем разделе содержатся общие функции [промкл](https://prometheus.io/docs/prometheus/latest/querying/basics/) . Эти функции можно использовать для анализа метрик, принимаемых в Azure обозреватель данных системой мониторинга [Prometheus](https://prometheus.io/) . Все функции предполагают, что метрики в Azure обозреватель данных структурированы с помощью [модели данных Prometheus](https://prometheus.io/docs/concepts/data_model/).


|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[series_metric_fl ()](series-metric-fl.md)|Выбирает и получает временные ряды, хранящиеся в модели данных Prometheus. |
|[series_rate_fl ()](series-rate-fl.md)|Вычисляет среднюю частоту увеличения метрики счетчика в секунду. |

## <a name="series-processing-functions"></a>Функции обработки рядов

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Столбцы метрик квантуем. |
|[series_dot_product_fl()](series-dot-product-fl.md)|Вычисляет скалярное произведение двух числовых векторов. |
|[series_downsample_fl()](series-downsample-fl.md)|Понижение временных рядов с помощью целочисленного фактора. |
|[series_exp_smoothing_fl()](series-exp-smoothing-fl.md)|Применяет базовый фильтр по экспоненциальному смягчению к ряду. |
|[series_fit_lowess_fl()](series-fit-lowess-fl.md)|Соответствует локальному полиному для ряда с помощью метода ЛОВЕСС. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Соответствует полиному для ряда с помощью регрессионного анализа. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Применяет фильтр скользящего среднего к ряду. |
|[series_rolling_fl()](series-rolling-fl.md)|Применяет функцию последовательного агрегирования для ряда. |
