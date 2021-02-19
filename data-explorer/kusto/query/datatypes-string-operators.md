---
title: Оператор String — Azure Data Explorer
description: В этой статье описываются операторы String в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 9e8197d3af25da0b0e2488b4a5c70f5cfa2dde17
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528077"
---
# <a name="string-operators"></a>Строковые операторы

Kusto предлагает различные операторы запросов для поиска строковых типов данных. В следующей статье описывается индексирование строковых терминов, приводится список строковых операторов запроса и даются советы по оптимизации производительности.

## <a name="understanding-string-terms"></a>Общие сведения о строковых терминах

Kusto индексирует все столбцы, включая столбцы типа `string`. В зависимости от фактических данных для таких столбцов создается несколько индексов. Эти индексы не предоставляются напрямую. Они используются в запросах с операторами `string`, в имени которых содержится `has`, например `has`, `!has`, `hasprefix`, `!hasprefix`. Семантика этих операторов определяется способом кодирования столбца. Вместо того чтобы выполнять "обычное" сопоставление подстроки, эти операторы сопоставляют *термины*.

### <a name="what-is-a-term"></a>Что такое термин? 

По умолчанию каждое значение `string` разбивается на максимальные последовательности буквенно-цифровых символов ASCII, и каждая из этих последовательностей превращается в термин.
Например, в следующем операторе `string`терминами являются `Kusto`, `KustoExplorerQueryRun`и следующие подстроки: `ad67d136`, `c1db`, `4f9f`, `88ef`, `d94f3b6b0b5a`.

```
Kusto: ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;KustoExplorerQueryRun
```

Kusto создает индекс терминов, состоящий из всех терминов, *которые составляют три или более символов*, и этот индекс используется `has`, `!has` и т. д.  Если запрос ищет термин, который содержит менее трех символов или использует оператор `contains`, запрос продолжит проверять значения в столбце. Проверка выполняется намного медленнее, чем поиск термина в индексе терминов.

> [!NOTE]
> В EngineV2 термин состоит из четырех или более символов.

## <a name="operators-on-strings"></a>Операторы в строках

> [!NOTE]
> В таблице используются следующие сокращения:
> * ПЧ = правая часть выражения
> * ЛЧ = левая часть выражения
> 
> Операторы с суффиксом `_cs` учитывают регистр.

> [!NOTE]
> Операторы, работающие без учета регистра, сейчас поддерживают только текст ASCII. Для сравнения работы со схемами, отличными от ASCII, используйте функцию [tolower()](tolowerfunction.md).

Оператор        |Описание                                                       |С учетом регистра|Пример (при `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Равно                                                            |Да           |`"aBc" == "aBc"`
`!=`            |Не равно                                                        |Да           |`"abc" != "ABC"`
`=~`            |Равно                                                            |Нет            |`"abc" =~ "ABC"`
`!~`            |Не равно                                                        |Нет            |`"aBc" !~ "xyz"`
`has`           |Правая часть представляет собой все слово в левой части     |Нет            |`"North America" has "america"`
`!has`          |ПЧ не является полным термином в ЛЧ                                     |Нет            |`"North America" !has "amer"` 
[`has_all`](has-all-operator.md)       |Аналогичен `has`, но работает со всеми элементами                    |Нет            |`"North and South America" has_all("south", "north")`
[`has_any`](has-anyoperator.md)       |Аналогичен `has`, но работает с любыми элементами                    |Нет            |`"North America" has_any("south", "north")`
`has_cs`        |ПЧ представляет целый термин в ЛЧ                                        |Да           |`"North America" has_cs "America"`
`!has_cs`       |ПЧ не является полным термином в ЛЧ                                     |Да           |`"North America" !has_cs "amer"` 
`hasprefix`     |ПЧ является префиксом термина в ЛЧ                                       |Нет            |`"North America" hasprefix "ame"`
`!hasprefix`    |ПЧ не является префиксом термина в ЛЧ                                   |Нет            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |ПЧ является префиксом термина в ЛЧ                                       |Да           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |ПЧ не является префиксом термина в ЛЧ                                   |Да           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |ПЧ является суффиксом термина в ЛЧ                                       |Нет            |`"North America" hassuffix "ica"`
`!hassuffix`    |ПЧ не является суффиксом термина в ЛЧ                                   |Нет            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |ПЧ является суффиксом термина в ЛЧ                                       |Да           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |ПЧ не является суффиксом термина в ЛЧ                                   |Да           |`"North America" !hassuffix_cs "icA"`
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
[`in`](inoperator.md)            |Соответствует одному из элементов                                     |Да           |`"abc" in ("123", "345", "abc")`
[`!in`](inoperator.md)           |Не соответствует одному из элементов                                 |Да           |`"bca" !in ("123", "345", "abc")`
`in~`           |Соответствует одному из элементов                                     |Нет            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |Не соответствует одному из элементов                                 |Нет            |`"bca" !in~ ("123", "345", "ABC")`


> [!TIP]
> Все операторы, содержащие поиск с `has` по индексированным *терминам* размером более четырех символов, а не по соответствиям подстрок. Термин создается путем разбиения строки на последовательности буквенно-цифровых символов ASCII. См. раздел [Общие сведения о строковых терминах](#understanding-string-terms).

## <a name="performance-tips"></a>Советы по улучшению производительности

Для повышения производительности при наличии двух операторов, которые выполняют одну и ту же задачу, используйте тот, который учитывает регистр.
Пример:

* вместо`=~` используйте `==`.
* вместо`in~` используйте `in`.
* вместо`contains` используйте `contains_cs`.

Для ускорения результатов при тестировании на наличие символа или буквенно-цифрового слова, которое связано с символами, отличными от алфавитных символов, или в начале или в конце поля используйте `has` или `in`. 
`has` выполняется быстрее, чем `contains`, `startswith`или `endswith`.

Например, первый из этих запросов будет выполняться быстрее:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
