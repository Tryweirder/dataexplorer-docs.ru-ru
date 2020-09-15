---
title: Библиотека функций — Azure обозреватель данных
description: В этой статье описываются определяемые пользователем функции, которые расширяют возможности Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 5b3457d52be37d4c0090db2f34c89994bc829a53
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2020
ms.locfileid: "90067544"
---
# <a name="functions-library"></a>Библиотека функций

В следующей статье содержится список определяемых пользователем функций по категориям.

## <a name="machine-learning-functions"></a>Функции машинного обучения

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl ()](predict-fl.md)|Прогнозирование с использованием существующей обученной модели машинного обучения. |
|[predict_onnx_fl ()](predict-onnx-fl.md)| Прогнозирование с использованием существующей обученной модели машинного обучения в формате ONNX. |

## <a name="series-processing-functions"></a>Функции обработки рядов

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl ()](quantize-fl.md)|Столбцы метрик квантуем. |
|[series_fit_poly_fl ()](series-fit-poly-fl.md)|Подгонка ряда от полинома с помощью регрессионного анализа. |
|[series_moving_avg_fl ()](series-moving-avg-fl.md)|Применяет фильтр скользящего среднего к ряду. |
|[series_rolling_fl ()](series-rolling-fl.md)|Применяет функцию последовательного агрегирования для ряда. |
