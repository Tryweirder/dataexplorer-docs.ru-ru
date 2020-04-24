---
title: array_index_of () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны array_index_of () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f68c9385c55cedb4491033d137af087dafd26aa0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518621"
---
# <a name="array_index_of"></a>array_index_of()

Поиск массива для указанного элемента и возвращает его позицию.

**Синтаксис**

`array_index_of(`*массив,**значение*`)`

**Аргументы**

* *массив*: Массив ввода для поиска.
* *значение*: Значение для поиска. Значение должно быть типа длинный, целый, двойной, дата, timespan, десятичная, строка или гид.

**Возвращает**

Нулевая индексная позиция поиска.
Возвращает -1, если значение не найдено в массиве.

**Пример**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Результат|
|---|
|3|

**См. также:**

Если вы только хотите проверить, существует ли значение в массиве, но вы не заинтересованы в его положении, вы можете использовать [set_has_element (arr, значение)](sethaselementfunction.md) - это улучшит читаемость вашего запроса, но производительность мудрый обе функции одинаковы.