---
title: series_less_equals () — обозреватель данных Azure
description: В этой статье описывается series_less_equals () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 8f86d7174e73f2ffbace935f695c1220572a0e38
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372601"
---
# <a name="series_less_equals"></a>series_less_equals()

Вычисляет операцию логического элемента "меньше или равно" ( `<=` ) с двумя входными числовыми последовательностями.

**Синтаксис**

`series_less_equals (`*Series1* `,` *Series2*`)`

**Аргументы**

* *Series1, Series2*. входные числовые массивы для поэлементного сравнения. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив логических значений, который содержит вычисляемую логическую операцию "меньше или равно" между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_equals_s2 = series_less_equals(s1, s2)
```

|s1|s2|s1_less_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true, true, false]|

**См. также:**

Сравнение статистики по всем рядам см. в следующих статьях:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
