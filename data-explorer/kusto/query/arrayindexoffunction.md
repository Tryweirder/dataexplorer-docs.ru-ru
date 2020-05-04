---
title: array_index_of () — обозреватель данных Azure
description: В этой статье описывается array_index_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: 27b956ee54ef22f55b3a0ceae97fceb41aadf5c3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737357"
---
# <a name="array_index_of"></a>array_index_of()

Выполняет поиск заданного элемента в массиве и возвращает его позицию.

**Синтаксис**

`array_index_of(`*массив*,*значение*`)`

**Аргументы**

* *массив*: входной массив для поиска.
* *значение*: искомое значение. Значение должно иметь тип long, Integer, Double, DateTime, TimeSpan, Decimal, String или GUID.

**Возвращает**

Отсчитываемая от нуля позиция индекса поиска.
Возвращает-1, если значение не найдено в массиве.

**Пример**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Результат|
|---|
|3|

**См. также:**

Если требуется только проверить, существует ли значение в массиве, но вы не заинтересованы в его положении, можно использовать [set_has_element (`arr`, `value`)](sethaselementfunction.md). Эта функция повысит удобочитаемость запроса. Обе функции имеют одинаковую производительность.
