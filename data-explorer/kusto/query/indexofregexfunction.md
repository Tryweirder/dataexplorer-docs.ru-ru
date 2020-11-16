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
ms.openlocfilehash: 5826920a411235166002b6833ed88869fb85f211
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638977"
---
# <a name="indexof_regex"></a>indexof_regex()

Возвращает отсчитываемый от нуля индекс первого вхождения указанного регулярного выражения поиска в пределах входной строки.

См. раздел [`indexof()`](indexoffunction.md).

## <a name="syntax"></a>Синтаксис

`indexof_regex(`*Исходный код* `,` *Уточняющий запрос* `[,` *start_index* `[,` *Длина* `[,` *вхождение*`]]])`

## <a name="arguments"></a>Аргументы

|Аргументы     | Описание                                     |Обязательный или необязательный|
|--------------|-------------------------------------------------|--------------------|
|source        | Входная строка                                    |Обязательно            |
|уточняющий запрос        | Строка подстановки регулярных выражений.               |Обязательно            |
|start_index   | Поиск начального расположения                           |Необязательно            |
|length        | Число позиций символов для проверки. -1 определяет неограниченную длину |Необязательно            |
|occurrence    | Найдите индекс N-го вида шаблона. 
                 Значение по умолчанию — 1, индекс первого вхождения |Необязательно            |

## <a name="returns"></a>Возвращаемое значение

Отсчитываемая от нуля позиция индекса *поиска*.

* Возвращает значение-1, если строка не найдена во входных данных.
* Возвращает *значение NULL* , если:
     * start_index меньше 0.
     * повторение меньше 0.
     * параметр длины меньше-1.

> [!NOTE]
- Поиск перекрывающихся совпадений не поддерживается.
- Строки в регулярных выражениях могут содержать символы, для которых требуется экранирование или использование литералов @ ' '.

## <a name="examples"></a>Примеры

```kusto
print
 idx1 = indexof_regex("abcabc", @"a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", @"a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", @"a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", @"a.a", 0, -1, 2)  // Matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", @"a|ab", -1)  // invalid start_index argument
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
