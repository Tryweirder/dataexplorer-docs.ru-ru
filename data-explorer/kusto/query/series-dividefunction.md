---
title: series_divide() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны series_divide () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8e8b806c325da9bfce5f79ce5a5c4e5cfadaa838
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508846"
---
# <a name="series_divide"></a>series_divide()

Вычисляет элементомудрое деление двух числовых вводов серии.

**Синтаксис**

`series_divide(`*серия1* `,` *серия2*`)`

**Аргументы**

* *series1, series2*: Числовые массивы ввода, первые, которые будут элементоскуты разделены на второй на динамический результат массива. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив расчетливой операции разделения элементов между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

Примечание: серия результатов имеет двойной тип, даже если входные данные являются рядами. Разделение на ноль следует за двойным делением на ноль (например, 2/0 дает двойной (inf)).

**Пример**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [0.25,1.0,4.0]|
|[2,4,8]    |[8,4,2]|   [0.25,1.0,4.0]|
|[3,6,12]   |[12,6,3]|  [0.25,1.0,4.0]|