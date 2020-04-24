---
title: series_stats () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны series_stats () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 07aa5df7351a5d4be1522d39456423197bde508d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507928"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`возвращает статистику для серии в нескольких столбцах.  

Функция `series_stats()` берет столбец, содержащий динамический числовой массив в качестве ввода, и вычисляет следующие столбцы:
* `min`: минимальное значение в массиве ввода
* `min_idx`: первое положение минимального значения в массиве ввода
* `max`: максимальное значение в массиве ввода
* `max_idx`: первое положение максимального значения в массиве ввода
* `avg`: среднее значение массива ввода
* `variance`: дисперсия образца вхоточного массива
* `stdev`: стандартное отклонение образца массива ввода

> [!NOTE] 
> Эта функция возвращает несколько столбцов, поэтому она не может быть использована в качестве аргумента для другой функции.

**Синтаксис**

project `series_stats(` *x* `[,` *ignore_nonfinite* `])` или `series_stats(`расширяет *x* `)` Returns все вышеупомянутые столбцы со следующими названиями: series_stats_x_min, series_stats_x_min_idx и т.д.
 
проект (м,`series_stats(`ми)*х* `)` или расширение (м, ми)`series_stats(`*x* `)` Возвращает следующие столбцы: м (мин) и ми (min_idx).

**Аргументы**

* *x*: Динамическая ячейка массива, представляющая собой массив числовых значений. 
* *ignore_nonfinite*: Boolean (необязательно, по умолчанию: `false`) флаг, который определяет, следует ли вычислить статистику, игнорируя при этом неконечные значения *(null*, *NaN*, *inf*и т.д.). Если установить, `false`то возвращенные `null` значения будут, если в массиве присутствуют неконечные значения.

**Пример**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32.8|28.5036338535483|812.457142857143|
