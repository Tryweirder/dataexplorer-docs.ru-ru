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
ms.openlocfilehash: 99044d8762a1c7c7e86fb2633a8226ef48d66b55
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225638"
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Результат|
|---|
|3|

**См. также:**

Если требуется только проверить, существует ли значение в массиве, но вы не заинтересованы в его положении, можно использовать [set_has_element ( `arr` , `value` )](sethaselementfunction.md). Эта функция повысит удобочитаемость запроса. Обе функции имеют одинаковую производительность.
