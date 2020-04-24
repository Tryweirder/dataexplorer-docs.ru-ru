---
title: indexof() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается indexof() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 698cc7c13c3d665f9f5cfe25a31269dc763c51fb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513946"
---
# <a name="indexof"></a>indexof()

Функция сообщает нулевой индекс первого появления указанной строки в строке ввода.

Если поиск или строка ввода не имеет типа строки - принудительно отбрасывает значение для строки.

Смотрите [`indexof_regex()`](indexofregexfunction.md).

**Синтаксис**

`indexof(`*источник*`,`*поиска*`[,`*start_index*`[,`*возникновения* *длины*`[,``]]])`

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
 idx1 = indexof("abcdefg","cde")    // lookup found in input string
 , idx2 = indexof("abcdefg","cde",1,4) // lookup found in researched range 
 , idx3 = indexof("abcdefg","cde",1,2) // search starts from index 1, but stops after 2 chars, so full lookup can't be found
 , idx4 = indexof("abcdefg","cde",3,4) // search starts after occurrence of lookup
 , idx5 = indexof("abcdefg","cde",-1)  // invalid input
 , idx6 = indexof(1234567,5,1,4)       // two first parameters were forcibly casted to strings "12345" and "5"
 , idx7 = indexof("abcdefg","cde",2,-1)  // lookup found in input string
 , idx8 = indexof("abcdefgabcdefg", "cde", 1, 10, 2)   // lookup found in input range
 , idx9 = indexof("abcdefgabcdefg", "cde", 1, -1, 3)   // the third occurrence of lookup is not in researched range
```

|idx1|idx2|idx3|idx4|idx5|idx6|idx7|idx8|idx9|
|----|----|----|----|----|----|----|----|----|
|2   |2   |-1  |-1  |    |4   |2   |9   |-1  |
