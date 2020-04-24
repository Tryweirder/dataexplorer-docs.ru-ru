---
title: series_add() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны series_add () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ea8c391060e487413a166c236abf789edcba0a0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509067"
---
# <a name="series_add"></a>series_add()

Вычисляет элемент-мудрый добавление двух входов численной серии.

**Синтаксис**

`series_add(`*серия1* `,` *серия2*`)`

**Аргументы**

* *series1, series2*: Числовые наборы для элементов, добавленные в результат динамического массива. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив расчетливого элемента-мудрый добавить операции между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[5,4,5]|
|[2,4,8]|[8,4,2]|[10,8,10]|
|[3,6,12]|[12,6,3]|[15,12,15]|