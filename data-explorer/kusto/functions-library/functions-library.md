---
title: Библиотека функций — Azure обозреватель данных
description: В этой статье описываются определяемые пользователем функции, которые расширяют возможности Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fb08edf4105c44a6be96cf6b2f314cf25887e69a
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444000"
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
