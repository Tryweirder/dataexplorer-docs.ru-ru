---
title: series_not_equals () — обозреватель данных Azure
description: В этой статье описывается series_not_equals () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 7b17d9b7150d6d58ae3b3b3be7abf83dc9979038
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372554"
---
# <a name="series_not_equals"></a>series_not_equals()

Вычисляет операцию логического элемента Not Equals ( `!=` ) с двумя входными числовыми последовательностями.

**Синтаксис**

`series_not_equals (`*Series1* `,` *Series2*`)`

**Аргументы**

* *Series1, Series2*. входные числовые массивы для поэлементного сравнения. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив логических значений, содержащий вычисляемую операцию поэлементного выполнения не равной логике между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true, false, true]|

**См. также:**

Сравнение статистики по всем рядам см. в следующих статьях:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
