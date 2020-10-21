---
title: indexof_regex () — обозреватель данных Azure
description: В этой статье описывается indexof_regex () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 472fdea209cc416893043f15b3796862ef91fa82
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243283"
---
# <a name="indexof_regex"></a>indexof_regex()

Возвращает отсчитываемый от нуля индекс первого вхождения указанной строки во входной строке. Обычные совпадения строк не перекрываются.

См. раздел [`indexof()`](indexoffunction.md).

## <a name="syntax"></a>Синтаксис

`indexof_regex(`*Исходный код* `,` *Уточняющий запрос* `[,` *start_index* `[,` *Длина* `[,` *вхождение*`]]])`

## <a name="arguments"></a>Аргументы

|Аргументы     | Описание                                     |Обязательный или необязательный|
|--------------|-------------------------------------------------|--------------------|
|source        | Входная строка                                    |Обязательно            |
|уточняющий запрос        | Искомая строка                                  |Обязательно            |
|start_index   | Поиск начального расположения                           |Необязательно            |
|length        | Число позиций символов для проверки. -1 определяет неограниченную длину |Необязательно            |
|occurrence    | Найдите индекс N-го вида шаблона. 
                 Значение по умолчанию — 1, индекс первого вхождения |Необязательно            |

## <a name="returns"></a>Результаты

Отсчитываемая от нуля позиция индекса *поиска*.

* Возвращает значение-1, если строка не найдена во входных данных.
* Возвращает *значение NULL* , если:
     * start_index меньше 0.
     * повторение меньше 0.
     * параметр длины меньше-1.


## <a name="examples"></a>Примеры

```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
