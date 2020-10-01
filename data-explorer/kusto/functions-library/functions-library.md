---
title: Библиотека функций — Azure обозреватель данных
description: В этой статье описываются определяемые пользователем функции, которые расширяют возможности Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 8cdccf261f755a0ea7a3d6a6299aa54ce021f366
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614988"
---
# <a name="functions-library"></a>Библиотека функций

В приведенной ниже статье содержится список [определяемых пользователем функций типа UDF](../query/functions/user-defined-functions.md).

Код определяемых пользователем функций указан в статьях.  Он может использоваться в инструкции Let, внедренной в запрос, или может быть сохранен в базе данных с помощью [функции. Create](../management/create-function.md).

## <a name="machine-learning-functions"></a>Функции машинного обучения

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl()](predict-fl.md)|Прогнозирование с использованием существующей обученной модели машинного обучения. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Прогнозирование с использованием существующей обученной модели машинного обучения в формате ONNX. |

## <a name="series-processing-functions"></a>Функции обработки рядов

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Столбцы метрик квантуем. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Подгонка ряда от полинома с помощью регрессионного анализа. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Применяет фильтр скользящего среднего к ряду. |
|[series_rolling_fl()](series-rolling-fl.md)|Применяет функцию последовательного агрегирования для ряда. |
