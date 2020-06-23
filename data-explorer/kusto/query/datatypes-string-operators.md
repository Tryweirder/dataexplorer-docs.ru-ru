---
title: Строковые операторы — Azure обозреватель данных
description: В этой статье описываются строковые операторы в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6718ad614166d5328dcd412d09405c1db8cc14dc
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265088"
---
# <a name="string-operators"></a>Строковые операторы

В следующей таблице перечислены операторы в строках.

Оператор        |Описание                                                       |С учетом регистра|Пример (при `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Равно                                                            |Да           |`"aBc" == "aBc"`
`!=`            |Не равно                                                        |Да           |`"abc" != "ABC"`
`=~`            |Равно                                                            |Нет            |`"abc" =~ "ABC"`
`!~`            |Не равно                                                        |Нет            |`"aBc" !~ "xyz"`
`has`           |Правая часть представляет собой все слово в левой части     |Нет            |`"North America" has "america"`
`!has`          |RHS не является полным условием в LHS                                     |Нет            |`"North America" !has "amer"` 
`has_cs`        |Правая часть представляет собой все слово в левой части     |Да           |`"North America" has_cs "America"`
`!has_cs`       |RHS не является полным условием в LHS                                     |Да           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS — префикс термина в LHS                                       |Нет            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS не является префиксом термина в LHS                                   |Нет            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS — префикс термина в LHS                                       |Да           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS не является префиксом термина в LHS                                   |Да           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS — это суффикс термина в LHS                                       |Нет            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS — это не суффикс термина в LHS                                   |Нет            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS — это суффикс термина в LHS                                       |Да           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS — это не суффикс термина в LHS                                   |Да           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS возникает как последовательность LHS                                |Нет            |`"FabriKam" contains "BRik"`
`!contains`     |RHS не возникает в LHS                                         |Нет            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS возникает как последовательность LHS                                |Да           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS не возникает в LHS                                         |Да           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS является начальной последовательностью LHS                              |Нет            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS не является начальной последовательностью LHS                          |Нет            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS является начальной последовательностью LHS                              |Да           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS не является начальной последовательностью LHS                          |Да           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS является закрывающей последовательностью LHS                               |Нет            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS не является закрывающей последовательностью LHS                           |Нет            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS является закрывающей последовательностью LHS                               |Да           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS не является закрывающей последовательностью LHS                           |Да           |`"Fabrikam" !endswith "brik"`
`matches regex` |Левая часть содержит соответствие для правой части                                      |Да           |`"Fabrikam" matches regex "b.*k"`
`in`            |Соответствует одному из элементов                                     |Да           |`"abc" in ("123", "345", "abc")`
`!in`           |Не соответствует одному из элементов                                 |Да           |`"bca" !in ("123", "345", "abc")`
`in~`           |Соответствует одному из элементов                                     |Нет            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |Не соответствует одному из элементов                                 |Нет            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |То же, что и `has` для любого из элементов                    |Нет            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>Советы по улучшению производительности

Для повышения производительности при наличии двух операторов, которые выполняют одну и ту же задачу, используйте учет регистра.
Пример:

* вместо `=~` Используйте`==`
* вместо `in~` Используйте`in`
* вместо `contains` Используйте`contains_cs`

Для ускорения результатов при тестировании на наличие символа или буквенно-цифрового слова, которое связано с символами, отличными от алфавитных символов, или в начале или в конце поля используйте `has` или `in` . 
`has`работает быстрее `contains` , чем, `startswith` или `endswith` .

Например, первый из этих запросов будет выполняться быстрее:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>Основные сведения об строковых терминах

По умолчанию Kusto индексирует все столбцы, включая столбцы типа `string` .
Для таких столбцов создается несколько индексов в зависимости от фактических данных. Эти индексы не предоставляются напрямую (кроме положительного влияния на производительность запросов), за исключением `string` операторов, которые `has` входят в состав имени, например,, `has` `!has` `hasprefix` , `!hasprefix` .
Эти операторы являются специальными, поскольку их семантика определяется способом кодирования столбца. Вместо того чтобы выполнять "простые" совпадения подстрок, эти операторы соответствуют **условиям**.

Чтобы понять совпадение на основе термина, необходимо сначала понять, что такое термин. По умолчанию каждое `string` значение разбивается на максимальные последовательности буквенно-цифровых символов ASCII, и каждая из этих последовательностей выполняется в термине.

Например, в следующем примере `string` термины являются `Kusto` , `WilliamGates3rd` и следующие подстроки: `ad67d136` , `c1db` , `4f9f` , `88ef` , `d94f3b6b0b5a` .

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

По умолчанию Kusto создает индекс термина, состоящий из всех терминов, состоящих из **четырех или более символов**, и этот индекс используется в `has` , `!has` и т. д. при поиске терминов, которые также имеют четыре символа или больше.
Если запрос ищет термин, размер которого меньше четырех символов, или `contains` оператор, Kusto вернется к просмотру значений в столбце, если он не может определить соответствие. Этот метод намного медленнее, чем поиск термина в индексе термина.
