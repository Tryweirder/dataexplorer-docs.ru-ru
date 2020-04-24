---
title: оператор поиска - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается оператор поиска в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de63aa3fde421996809334b8a746ea4dee8cb026
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509203"
---
# <a name="search-operator"></a>Оператор search

Оператор поиска предоставляет многостоловый/многокомпонентный поиск.

## <a name="syntax"></a>Синтаксис

* -*ТабулярныйИсточник* `|` `search` -`kind=`*CaseSensitivity*`in` `(`- *SearchPredicate* *ТаблицаИсточники*`)`

## <a name="arguments"></a>Аргументы

* *TabularSource*: Дополнительное табулярное выражение, которое выступает в качестве источника данных для поиска, например, название таблицы, [оператор профсоюза,](unionoperator.md)результаты табликового запроса и т.д. Не может появиться вместе с дополнительной фразой, которая включает в себя *TableSources*.

* *CaseSensitivity*: Дополнительный флаг, который `string` контролирует поведение всех операторов scalar в отношении чувствительности случая. Допустимые значения являются двумя `default` синонимами и `case_insensitive` (что является `has`по умолчанию для операторов, `case_sensitive` таких как, а именно нечувствительным случаем) и (что заставляет всех таких операторов в режим сопоставления случаев).

* *TableSources*: Дополнительный список запятой, разделенных с именами "wildcarded" для участия в поиске.
  Список имеет тот же синтаксис, что и список [оператора союза.](unionoperator.md)
  Не может появиться вместе с дополнительным *TabularSource*.

* *SearchPredicate*: Обязательный предикат, который определяет, что искать (другими словами, выражение Boolean, которое оценивается для каждой записи в входе, и что, если он возвращается `true`, запись выводилась.) Синтаксис для *SearchPredicate* расширяет и изменяет обычный синтаксис Kusto для выражений Boolean:

  **Строка соответствия расширений**: Строка буквальные, которые появляются в виде терминов в *SearchPredicate* указать термин матч между всеми столбцами и буквальное использование `has` `hasprefix`, , `hassuffix`и инвертированные (`!`) или случае чувствительных ( )`sc`версии этих операторов. Решение о том, `hasprefix`следует `hassuffix` ли применять `has`, или зависит от того, буквально начинается или заканчивается (или оба) звездочкой ().`*` Звездочки внутри буквального не допускаются.

    |Литерал   |Оператор   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **Ограничение столбца**: По умолчанию, строка соответствия расширения попытка сопоставить со всеми столбцов набора данных. Это соответствие можно ограничить определенным столбецом, используя следующий синтаксис: *ColumnName*`:`*StringLiteral.*

  **Равенство строк**: Точные совпадения столбца со значением строки (вместо термина-матча) можно сделать с помощью синтаксиса *ColumnName*`==`*StringLiteral.*

  **Другие выражения Булеан**: Все регулярные выражения Кусто Булеан поддерживаются синтаксисом.
    Например, `"error" and x==123` означает: поиск записей, `error` которые имеют термин в любом `123` из `x` своих столбцов, и имеют значение в столбце".

  **Regex матч**: Регулярное соответствие выражения указывается с помощью *колонки* `matches regex` *StringLiteral* синтаксис, где *StringLiteral* является шаблон regex.

Обратите внимание, что при опущении *TabularSource* и *TableSources,* поиск переносится по всем неограниченным таблицам и представлениям базы данных в области охвата.

## <a name="summary-of-string-matching-extensions"></a>Резюме расширения строки, сопрягающие строки

  |# |Синтаксис                                 |Значение (эквивалент) `where`           |Комментарии|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab\w*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |Все сравнения строк чувствительны к случаям|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>Примечания

**В отличие от** `search` оператора [поиска,](findoperator.md)оператор не поддерживает следующее:

1. `withsource=`Вывод всегда будет включать столбец, называемый `$table` типом, `string` значение которого является именем таблицы, из которого была извлечена каждая запись (или каким-либо системным именем, если источник не является таблицей, а составным выражением).
2. `project=`, `project-smart`: Схема вывода эквивалентна `project-smart` схеме вывода.

## <a name="examples"></a>Примеры

```kusto
// 1. Simple term search over all unrestricted tables and views of the database in scope
search "billg"

// 2. Like (1), but looking only for records that match both terms
search "billg" and ("steveb" or "satyan")

// 3. Like (1), but looking only in the TraceEvent table
search in (TraceEvent) and "billg"

// 4. Like (2), but performing a case-sensitive match of all terms
search "BillB" and ("SteveB" or "SatyaN")

// 5. Like (1), but restricting the match to some columns
search CEO:"billg" or CSA:"billg"

// 6. Like (1), but only for some specific time limit
search "billg" and Timestamp >= datetime(1981-01-01)

// 7. Searches over all the higher-ups
search in (C*, TF) "billg" or "davec" or "steveb"

// 8. A different way to say (7). Prefer to use (7) when possible
union C*, TF | search "billg" or "davec" or "steveb"
```

## <a name="performance-tips"></a>Советы по производительности

  |# |Подсказка                                                                                  |Prefer                                        |Over                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| Предпочитаю использовать одного `search` оператора в `search` течение нескольких последовательных операторов|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| Предпочитаю фильтровать `search` внутри оператора                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
