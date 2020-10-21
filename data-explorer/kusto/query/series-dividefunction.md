---
title: series_divide () — обозреватель данных Azure
description: В этой статье описывается series_divide () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 093f78cfc30da31474094187a786585fdcd5132b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252917"
---
# <a name="series_divide"></a>series_divide()

Вычисляет поэлементное деление двух входных числовых рядов.

## <a name="syntax"></a>Синтаксис

`series_divide(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Аргументы

* *Series1, Series2*: входные числовые массивы, первый из которых будет полагаться на поэлементное деление на секунду в результате динамического массива. Все аргументы должны быть динамическими массивами. 

## <a name="returns"></a>Результаты

Динамический массив вычисляемой операции деления на уровне элементов между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

Примечание. результирующий ряд имеет тип Double, даже если входные данные являются целыми числами. Деление на ноль следует за двойным делением на ноль (например, 2/0 возвращает значение Double (+ INF)).

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [0,25, 1.0, 4.0]|
|[2, 4, 8]    |[8, 4, 2]|   [0,25, 1.0, 4.0]|
|[3, 6, 12]   |[12, 6, 3]|  [0,25, 1.0, 4.0]|
