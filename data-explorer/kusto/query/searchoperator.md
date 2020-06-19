---
title: оператор поиска в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор поиска в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: edd35e5e259666e8ce4360c072aaac6717e6f8c3
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071866"
---
# <a name="search-operator"></a>Оператор search

Оператор поиска предоставляет возможность поиска в нескольких таблицах или с несколькими столбцами.

## <a name="syntax"></a>Синтаксис

* [*Табуларсаурце* `|` ] `search`[ `kind=` *Касесенситивити*] [ `in` `(` *таблесаурцес* `)` ] *сеарчпредикате*

## <a name="arguments"></a>Аргументы

* *Табуларсаурце*: необязательное табличное выражение, которое выступает в качестве источника данных для поиска, например имя таблицы, [оператор Union](unionoperator.md), результаты табличного запроса и т. д. Не может содержаться вместе с необязательной фразой, включающей *таблесаурцес*.

* *Касесенситивити*: необязательный флаг, который управляет поведением всех `string` скалярных операторов относительно чувствительности к регистру. Допустимыми значениями являются два синонима `default` и `case_insensitive` (которые используются по умолчанию для таких операторов `has` , как, без учета регистра) и `case_sensitive` (что приводит к принудительному включению всех таких операторов в режим сопоставления с учетом регистра).

* *Таблесаурцес*: необязательный разделенный запятыми список имен таблиц с подстановочными знаками, которые можно использовать в поиске.
  Список имеет тот же синтаксис, что и список [оператора UNION](unionoperator.md).
  Не может использоваться вместе с необязательным *табуларсаурце*.

* *Сеарчпредикате*: обязательный предикат, который определяет, что искать (иными словами, логическое выражение, вычисляемое для каждой записи во входных данных, и, если оно возвращает `true` , запись выводится на выходе). Синтаксис *сеарчпредикате* расширяет и изменяет стандартный синтаксис Kusto для логических выражений:

  **Расширения, совпадающие со строками**: строковые литералы, которые отображаются как термины в *сеарчпредикате* , указывают на совпадение терминов между всеми столбцами и литералом с помощью `has` , `hasprefix` , `hassuffix` и обратных ( `!` ) или с учетом регистра ( `sc` ) версий этих операторов. Решение, следует ли применять `has` , `hasprefix` или `hassuffix` зависит от того, начинается или заканчивается литерал (или оба) звездочкой ( `*` ). Звездочки внутри литерала не допускаются.

    |Литерал   |Оператор   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **Ограничение столбца**: по умолчанию модули сопоставления строк пытаются сопоставить все столбцы набора данных. Можно ограничить это сопоставление определенным столбцом, используя следующий синтаксис: *ColumnName* `:` *стринглитерал*.

  **Равенство строк**. точное совпадение столбца со строковым значением (вместо совпадения термина) можно выполнить с помощью синтаксиса *ColumnName* `==` *стринглитерал*.

  **Другие логические выражения**. синтаксис поддерживает все регулярные логические выражения Kusto.
    Например, `"error" and x==123` это означает: Поиск записей, имеющих термин `error` в любом из столбцов, и их значения `123` в `x` столбце ".

  **Соответствие**регулярного выражения: сопоставление регулярных выражений обозначается с помощью синтаксиса *Column* `matches regex` *стринглитерал* , где *стринглитерал* — шаблон Regex.

Обратите внимание, что если опущены оба *табуларсаурце* и *таблесаурцес* , Поиск переносится по всем неограниченным таблицам и представлениям базы данных в области.

## <a name="summary-of-string-matching-extensions"></a>Сводка по расширениям сопоставления строк

  |# |Синтаксис                                 |Значение (эквивалентное `where` )           |Комментарии|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab.*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |Все сравнения строк учитывают регистр|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>Remarks

В **отличие от** [оператора Find](findoperator.md), `search` оператор не поддерживает следующее:

1. `withsource=`: Выходные данные всегда будут включать столбец с именем `$table` типа `string` , значением которого является имя таблицы, из которой была получена каждая запись (или определенное системное имя, если источник не является таблицей, а составным выражением).
2. `project=`, `project-smart` : Выходная схема эквивалентна `project-smart` выходной схеме.

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

  |# |Совет                                                                                  |Prefer                                        |Over                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| Предпочитать использование одного `search` оператора для нескольких последовательных `search` операторов|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| Предпочитать фильтрацию внутри `search` оператора                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
