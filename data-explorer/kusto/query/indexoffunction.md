---
title: IndexOf () — Azure обозреватель данных
description: В этой статье описывается IndexOf () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1558e2463c2958965fcb501aff99c7ec14fe8688
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252956"
---
# <a name="indexof"></a>indexof()

Возвращает отсчитываемый от нуля индекс первого вхождения указанной строки во входной строке.

Если уточняющий запрос или входная строка не имеет *строкового* типа, функция принудительно приводит значение к типу *String*.

Дополнительные сведения см. в статье [`indexof_regex()`](indexofregexfunction.md).

## <a name="syntax"></a>Синтаксис

`indexof(`*Исходный код* `,` *Уточняющий запрос* `[,` *start_index* `[,` *Длина* `[,` *вхождение*`]]])`

## <a name="arguments"></a>Аргументы

* *источник*: входная строка.  
* *Уточняющий запрос*: строка для поиска.
* *start_index*: начальное расположение поиска. Необязательный элемент.
* *Длина*: число позиций символов для проверки. Значение-1 означает неограниченную длину. Необязательный элемент.
* *вхождение*: номер экземпляра. Значение по умолчанию — 1. Необязательный элемент.

## <a name="returns"></a>Результаты

Отсчитываемая от нуля позиция индекса *поиска*.

Возвращает значение-1, если строка не найдена во входных данных.

Если не имеет значения (меньше 0) *start_index*, *вхождение*или (меньше-1) параметра *длины* , возвращает *значение NULL*.

## <a name="examples"></a>Примеры
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
