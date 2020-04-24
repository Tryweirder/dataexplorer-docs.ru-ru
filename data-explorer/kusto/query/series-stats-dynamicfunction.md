---
title: series_stats_dynamic () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны series_stats_dynamic () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: b667af6d037b0b316bf18406e1fb49528e390213
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507911"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

Возвращает статистику для серии в динамическом объекте.  

`series_stats_dynamic()` Функция берет столбец, содержащий динамический числовой массив в качестве ввода, и генерирует динамическое значение со следующим содержанием:
* `min`: минимальное значение в массиве ввода
* `min_idx`: первое положение минимального значения в массиве ввода
* `max`: максимальное значение в массиве ввода
* `max_idx`: первое положение максимального значения в массиве ввода
* `avg`: среднее значение массива ввода
* `variance`: дисперсия образца вхоточного массива
* `stdev`: стандартное отклонение образца массива ввода

**Синтаксис**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**Аргументы**

* *x*: Динамическая ячейка массива, представляющая собой массив числовых значений. 
* *ignore_nonfinite*: Boolean (необязательно, по умолчанию: `false`) флаг, который определяет, следует ли вычислить статистику, игнорируя при этом неконечные значения *(null*, *NaN*, *inf*и т.д.). При `false` установке к `null` возвращенного результату, если в массиве присутствуют неконечные значения.

**Пример**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|"мин": 2.0, "min_idx": 8, "макс": 87.0, "max_idx": 3, "avg": 32.8, "stdev": 28.50363853548269, "разница": 812.45714285714