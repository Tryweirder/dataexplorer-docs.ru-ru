---
title: series_add () — обозреватель данных Azure
description: В этой статье описывается series_add () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b5847fec10eb76a6fe5a139809766d2a3ca4f089
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372919"
---
# <a name="series_add"></a>series_add()

Вычисляет Поэлементное добавление двух входных числовых рядов.

**Синтаксис**

`series_add(`*Series1* `,` *Series2*`)`

**Аргументы**

* *Series1, Series2*: входные числовые массивы, которые должны быть поэлементно добавлены в результат динамического массива. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив вычисляемого элемента операции добавления на основе двух входных значений. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[5, 4, 5]|
|[2, 4, 8]|[8, 4, 2]|[10, 8, 10]|
|[3, 6, 12]|[12, 6, 3]|[15, 12, 15]|
