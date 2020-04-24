---
title: series_less () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_less () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 8fc09141a6e60489ff2be246e145876357141785
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508268"
---
# <a name="series_less"></a>series_less()

Вычисляет элемент-мудрый меньше ()`<`логика операции двух численных ввода серии.

**Синтаксис**

`series_less (`*Серия1* `,` *Серия2*`)`

**Аргументы**

* *Series1, Series2*: Числовые наборы для ввода, чтобы быть по сравнению с элементами. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив булеанов, содержащий вычисленные элементы-мудрые менее логики операции между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_s2 = series_less(s1, s2)
```

|s1|s2|s1_less_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|(истинно, ложно, ложно)|

**См. также:**

Для сравнения статистики в целом серии см.:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)