---
title: series_subtract () — обозреватель данных Azure
description: В этой статье описывается series_subtract () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 388f24f12993bcdc91d86bfc3f3f20967e0b1cc5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372423"
---
# <a name="series_subtract"></a>series_subtract()

Вычисляет поэлементное вычитание двух входных числовых рядов.

**Синтаксис**

`series_subtract(`*Series1* `,` *Series2*`)`

**Аргументы**

* *Series1, Series2*: входные числовые массивы, второй — поэлементное вычитание из первого в результат динамического массива. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив вычисляемой операции вычитания по элементам между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[-3, 0, 3]|
|[2, 4, 8]|[8, 4, 2]|[-6, 0, 6]|
|[3, 6, 12]|[12, 6, 3]|[-9, 0, 9]|
