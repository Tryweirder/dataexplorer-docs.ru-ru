---
title: series_multiply () — обозреватель данных Azure
description: В этой статье описывается series_multiply () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 21095174a9b2f25a4c48fdd65c05579ed7efff7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246086"
---
# <a name="series_multiply"></a>series_multiply()

Вычисляет поэлементное умножение двух входных числовых рядов.

## <a name="syntax"></a>Синтаксис

`series_multiply(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Аргументы

* *Series1, Series2*: входные числовые массивы для поэлементного умножения в результат динамического массива. Все аргументы должны быть динамическими массивами. 

## <a name="returns"></a>Результаты

Динамический массив вычисляемой операции умножения на уровне элементов между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [4, 4, 4]|
|[2, 4, 8]    |[8, 4, 2]|   [16, 16, 16]|
|[3, 6, 12]   |[12, 6, 3]|  [36, 36, 36]|
