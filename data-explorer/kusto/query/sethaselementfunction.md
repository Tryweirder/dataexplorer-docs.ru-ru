---
title: set_has_element () — обозреватель данных Azure
description: В этой статье описывается set_has_element () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 9cf2ec4371f4aeef8a68cb65fb2b946b9c393054
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372377"
---
# <a name="set_has_element"></a>set_has_element()

Определяет, содержит ли указанный набор указанный элемент.

**Синтаксис**

`set_has_element(`*массив*,*значение*`)`

**Аргументы**

* *массив*: входной массив для поиска.
* *значение*: искомое значение. Значение должно иметь тип `long` , `integer` ,,, `double` `datetime` `timespan` , `decimal` , `string` или `guid` .

**Возвращает**

Значение true или false в зависимости от того, существует ли значение в массиве.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Результат|
|---|
|1|

**См. также:**

Если вы также заинтересованы в положении, где значение существует в массиве, можно использовать [array_index_of (ARR, Value)](arrayindexoffunction.md). Обе функции имеют одинаковые показатели производительности.
