---
title: series_greater () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны series_greater () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: b3f8be234cc196d55320bcace6bfca579c11a44c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508387"
---
# <a name="series_greater"></a>series_greater()

Вычисляет элемент-мудрый больше ()`>`логика операции двух численных ввода серии.

**Синтаксис**

`series_greater (`*Серия1* `,` *Серия2*`)`

**Аргументы**

* *Series1, Series2*: Числовые наборы для ввода, чтобы быть по сравнению с элементами. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив булеанов, содержащий вычисленная элементосчитанная большая логическая операция между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_s2 = series_greater(s1, s2)
```

|s1|s2|s1_greater_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|(ложный, ложный, истинный)|

**См. также:**

Для сравнения статистики в целом серии см.:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)