---
title: series_not_equals () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается series_not_equals () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ce9c695ececf1ac9f1fbe783ebe0fa35986f3d0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508183"
---
# <a name="series_not_equals"></a>series_not_equals()

Вычисляет элемент-мудрый`!=`не равен () логика операции двух численных ввода серии.

**Синтаксис**

`series_not_equals (`*Серия1* `,` *Серия2*`)`

**Аргументы**

* *Series1, Series2*: Числовые наборы для ввода, чтобы быть по сравнению с элементами. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив булеанов, содержащий вычисленное элементосчитанное не равное логическое функционирование между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|(истинно, ложно, верно)|

**См. также:**

Для сравнения статистики в целом серии см.:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)