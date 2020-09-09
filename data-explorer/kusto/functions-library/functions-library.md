---
title: Библиотека функций — Azure обозреватель данных
description: В этой статье описываются определяемые пользователем функции, которые расширяют возможности Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 66f8da1655fada7429fcd3087810904bd184546c
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614519"
---
# <a name="functions-library"></a>Библиотека функций

В следующей статье содержится список определяемых пользователем функций по категориям.

## <a name="machine-learning-functions"></a>Функции машинного обучения

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[predict_lf ()](predict-lf.md)|Прогнозирование с использованием существующей обученной модели машинного обучения. |
|[predict_onnx_lf ()](predict-onnx-lf.md)| Прогнозирование с использованием существующей обученной модели машинного обучения в формате ONNX. |

## <a name="series-processing-functions"></a>Функции обработки рядов

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_lf ()](quantize-lf.md)|Столбцы метрик квантуем. |
|[series_fit_poly_lf ()](series-fit-poly-lf.md)|Подгонка ряда от полинома с помощью регрессионного анализа. |
|[series_moving_avg_lf ()](series-moving-avg-lf.md)|Применяет фильтр скользящего среднего к ряду. |
|[series_rolling_lf ()](series-rolling-lf.md)|Применяет функцию последовательного агрегирования для ряда. |
