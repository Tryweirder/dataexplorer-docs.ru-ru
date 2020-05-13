---
title: series_greater () — обозреватель данных Azure
description: В этой статье описывается series_greater () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 7181e703ff6085d463f80bd169ae064066031d66
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372639"
---
# <a name="series_greater"></a>series_greater()

Вычисляет логику поэлементного выполнения ( `>` ) с двумя входными числовыми последовательностями.

**Синтаксис**

`series_greater (`*Series1* `,` *Series2*`)`

**Аргументы**

* *Series1, Series2*. входные числовые массивы для поэлементного сравнения. Все аргументы должны быть динамическими массивами. 

**Возвращает**

Динамический массив логических значений, содержащий вычисляемую поэлементную логику операций над двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_s2 = series_greater(s1, s2)
```

|s1|s2|s1_greater_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, false, true]|

**См. также:**

Сравнение статистики по всем рядам см. в следующих статьях:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
