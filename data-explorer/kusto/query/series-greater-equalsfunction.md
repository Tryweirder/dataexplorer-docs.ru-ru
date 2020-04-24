---
title: series_greater_equals () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_greater_equals () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 21a1e4d972c06f212344a665c6b3320046ee93c8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508404"
---
# <a name="series_greater_equals"></a>series_greater_equals()

Вычисляет элемент-мудрый больше`>=`или равен () логика операции двух численных ввода серии.

**Синтаксис**

`series_greater_equals (`*Серия1* `,` *Серия2*`)`

**Аргументы**

* *Series1, Series2*: Числовые наборы для ввода, чтобы быть по сравнению с элементами. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив булеанов, содержащий вычисленная элементоскурная большая или равная логическая операция между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_equals_s2 = series_greater_equals(s1, s2)
```

|s1|s2|s1_greater_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|(ложь, правда, правда)|

**См. также:**

Для сравнения статистики в целом серии см.:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)