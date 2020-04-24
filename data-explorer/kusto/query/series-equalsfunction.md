---
title: series_equals () - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает series_equals () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 267e9db8dd2980016f4022ce6358569f30aacb6f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508812"
---
# <a name="series_equals"></a>series_equals()

Вычисляет элемент-мудрый`==`равен () логика операции двух численных ввода серии.

**Синтаксис**

`series_equals (`*Серия1* `,` *Серия2*`)`

**Аргументы**

* *Series1, Series2*: Числовые наборы для ввода, чтобы быть по сравнению с элементами. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив булеанов, содержащий расчетливую операцию равной логики элемента между двумя входами. Любой нечислоный элемент или несуществующий элемент (массивы разных размеров) дает значение элемента. `null`

**Пример**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_equals_s2 = series_equals(s1, s2)
```

|s1|s2|s1_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|(ложный, истинный, ложный)|

**См. также:**

Для сравнения статистики в целом серии см.:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)