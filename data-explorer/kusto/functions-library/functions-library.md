---
title: Библиотека функций — Azure обозреватель данных
description: В этой статье описываются определяемые пользователем функции, которые расширяют возможности Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: ebde38055cb2d962b6007668117a55c779cc9c94
ms.sourcegitcommit: 88923cfb2495dbf10b62774ab2370b59681578b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92175508"
---
# <a name="functions-library"></a>Библиотека функций

В приведенной ниже статье содержится список [определяемых пользователем функций типа UDF](../query/functions/user-defined-functions.md).

Код определяемых пользователем функций указан в статьях.  Он может использоваться в инструкции Let, внедренной в запрос, или может быть сохранен в базе данных с помощью [функции. Create](../management/create-function.md).

## <a name="machine-learning-functions"></a>Функции машинного обучения

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl ()](kmeans-fl.md)|Кластеризации с использованием алгоритма k-средних. |
|[predict_fl()](predict-fl.md)|Прогнозирование с использованием существующей обученной модели машинного обучения. |
|[predict_onnx_fl()](predict-onnx-fl.md)| Прогнозирование с использованием существующей обученной модели машинного обучения в формате ONNX. |

## <a name="series-processing-functions"></a>Функции обработки рядов

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|Столбцы метрик квантуем. |
|[series_dot_product_fl ()](series-dot-product-fl.md)|Вычисляет скалярное произведение двух числовых векторов. |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|Соответствует полиному для ряда с помощью регрессионного анализа. |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|Применяет фильтр скользящего среднего к ряду. |
|[series_rolling_fl()](series-rolling-fl.md)|Применяет функцию последовательного агрегирования для ряда. |
