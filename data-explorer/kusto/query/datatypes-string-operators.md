---
title: Строковые операторы — Azure обозреватель данных
description: В этой статье описываются строковые операторы в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.openlocfilehash: 61ed36d5d1657957b38ea78f0d2e09faa3a3f4e9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247784"
---
# <a name="string-operators"></a>Строковые операторы

Kusto предлагает различные операторы запросов для поиска строковых типов данных. В следующей статье описывается индексирование строковых терминов, список операторов строкового запроса и даются советы по оптимизации производительности.

## <a name="understanding-string-terms"></a>Основные сведения об строковых терминах

Kusto индексирует все столбцы, включая столбцы типа `string` . Для таких столбцов создается несколько индексов в зависимости от фактических данных. Эти индексы не предоставляются напрямую, но используются в запросах с `string` операторами, которые являются `has` частью имени, например,, `has` `!has` `hasprefix` , `!hasprefix` . Семантика этих операторов определяется способом кодирования столбца. Вместо того чтобы выполнять "простые" совпадения подстрок, эти операторы соответствуют *условиям*.

### <a name="what-is-a-term"></a>Что такое термин? 

По умолчанию каждое `string` значение разбивается на максимальные последовательности буквенно-цифровых символов ASCII, и каждая из этих последовательностей выполняется в термине.
Например, в следующем примере `string` термины являются `Kusto` , `WilliamGates3rd` и следующие подстроки: `ad67d136` , `c1db` , `4f9f` , `88ef` , `d94f3b6b0b5a` .

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto создает индекс термина, состоящий из всех терминов, состоящих из *четырех или более символов*, и этот индекс используется в `has` , `!has` и т. д. Если запрос ищет термин, размер которого меньше четырех символов, или `contains` оператор, Kusto вернется к просмотру значений в столбце, если он не может определить соответствие. Этот метод намного медленнее, чем поиск термина в индексе термина.

## <a name="operators-on-strings"></a>Операторы в строках

> [!NOTE]
> Следующие сокращения используются в следующей таблице:
> * RHS = правая часть выражения
> * LHS = левая часть выражения

Оператор        |Описание                                                       |С учетом регистра|Пример (при `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Равно                                                            |Да           |`"aBc" == "aBc"`
`!=`            |Не равно                                                        |Да           |`"abc" != "ABC"`
`=~`            |Равно                                                            |Нет            |`"abc" =~ "ABC"`
`!~`            |Не равно                                                        |Нет            |`"aBc" !~ "xyz"`
`has`           |Правая часть представляет собой все слово в левой части     |Нет            |`"North America" has "america"`
`!has`          |RHS не является полным условием в LHS                                     |Нет            |`"North America" !has "amer"` 
`has_cs`        |RHS — это целый термин в LHS                                        |Да           |`"North America" has_cs "America"`
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
`endswith_cs`   |RHS является закрывающей последовательностью LHS                               |Да           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |RHS не является закрывающей последовательностью LHS                           |Да           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |LHS содержит совпадение для RHS                                      |Да           |`"Fabrikam" matches regex "b.*k"`
`in`            |Соответствует одному из элементов                                     |Да           |`"abc" in ("123", "345", "abc")`
`!in`           |Не соответствует одному из элементов                                 |Да           |`"bca" !in ("123", "345", "abc")`
`in~`           |Соответствует одному из элементов                                     |Нет            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |Не соответствует одному из элементов                                 |Нет            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |То же, что и `has` для любого из элементов                    |Нет            |`"North America" has_any("south", "north")`

> [!TIP]
> Все операторы `has` , содержащие Поиск по индексированным *условиям* четырех и более символов, а не совпадения подстрок. Термин создается путем разбиения строки на последовательности буквенно-цифровых символов ASCII. См. раздел [Основные сведения о строковых терминах](#understanding-string-terms).

## <a name="performance-tips"></a>Советы по улучшению производительности

Для повышения производительности при наличии двух операторов, которые выполняют одну и ту же задачу, используйте учет регистра.
Пример:

* вместо `=~` Используйте `==`
* вместо `in~` Используйте `in`
* вместо `contains` Используйте `contains_cs`

Для ускорения результатов при тестировании на наличие символа или буквенно-цифрового слова, которое связано с символами, отличными от алфавитных символов, или в начале или в конце поля используйте `has` или `in` . 
`has` работает быстрее `contains` , чем, `startswith` или `endswith` .

Например, первый из этих запросов будет выполняться быстрее:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
