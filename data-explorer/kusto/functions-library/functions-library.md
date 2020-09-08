---
title: Библиотека функций — Azure обозреватель данных
description: В этой статье описываются определяемые пользователем функции, расширяющие возможности Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 9089722123d1516970df54f0d0fb0fcba3d881e5
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557941"
---
# <a name="functions-library"></a>Библиотека функций

В следующей статье содержится список определяемых пользователем функций по категориям.

## <a name="series-processing-functions"></a>Функции обработки рядов

|Имя функции     |Описание                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_udf ()](quantize-udf.md)|Столбцы метрик квантуем. |
|[series_fit_poly_udf ()](series-fit-poly-udf.md)|Подгонка ряда от полинома с помощью регрессионного анализа. |
|[series_moving_avg_udf ()](series-moving-avg-udf.md)|Применяет фильтр скользящего среднего к ряду. |
|[series_rolling_udf ()](series-rolling-udf.md)|Применяет функцию последовательного агрегирования для ряда. |
