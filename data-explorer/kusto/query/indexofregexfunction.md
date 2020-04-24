---
title: indexof_regex () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается indexof_regex () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6da0523e85bab4883c50708ffe3f7d087fdd8c8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513895"
---
# <a name="indexof_regex"></a>indexof_regex()

Функция сообщает нулевой индекс первого появления указанной строки в строке ввода. Обычные совпадения строк не пересекаются. 

Смотрите [`indexof()`](indexoffunction.md).

**Синтаксис**

`indexof_regex(`*источник*`,`*поиска*`[,`*start_index*`[,`*возникновения* *длины*`[,``]]])`

**Аргументы**

* *источник*: строка ввода.  
* *поиск*: строка искать.
* *start_index*: позиция начала поиска (необязательно).
* *длина*: количество позиций символов для изучения, -1, определяющих неограниченную длину (необязательно).
* *возникновение*: является возникновение по умолчанию 1 (необязательно).

**Возвращает**

Нулевая индексная позиция *поиска.*

Возвращает -1, если строка не найдена в входе.
В случае нерелевантного (менее 0) *start_index,* *возникновение* или (менее -1) параметра *длины* - *возвращается нулевой*.


**Примеры**
```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5
|----|----|----|----|----
|0   |3   |-1  |-1  |    