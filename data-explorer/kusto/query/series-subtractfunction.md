---
title: series_subtract() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_subtract () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1e984bc35192da5d61448211c49ff582f225eb19
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507945"
---
# <a name="series_subtract"></a>series_subtract()

Вычисляет элемент-мудрый вычитание двух численных вводов серии.

**Синтаксис**

`series_subtract(`*серия1* `,` *серия2*`)`

**Аргументы**

* *series1, series2*: Числовые массивы ввода, второй, который будет элементоскуразительным вычтенным из первого в динамический результат массива. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив вычисляемых элементов вычитают операции между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[-3,0,3]|
|[2,4,8]|[8,4,2]|[-6,0,6]|
|[3,6,12]|[12,6,3]|[-9,0,9]|