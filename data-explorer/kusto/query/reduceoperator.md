---
title: Оператор сокращения — Azure обозреватель данных
description: В этой статье описывается оператор сокращения в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ef5e42dc9c41426cd66dbf4d857ec0d2c32e2ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252116"
---
# <a name="reduce-operator"></a>Оператор reduce

Группирует набор строк вместе на основе подобия значений.

```kusto
T | reduce by LogMessage with threshold=0.1
```

Для каждой такой группы выводится **шаблон** , наилучшим образом описывающий группу (возможно, с помощью `*` символа звездочкой () для представления подстановочных знаков **count** ), количество значений в группе и **репрезентативность** группы (одно из исходных значений в группе).

## <a name="syntax"></a>Синтаксис

*T* `|` `reduce` [ `kind` `=` *редуцекинд*] `by` *expr* [ `with` [ `threshold` `=` *threshold*] [ `,` `characters` `=` *символы*]]

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, результатом которого является `string` значение.
* *Threshold*— `real` литерал в диапазоне (0.. 1). Значение по умолчанию — 0,1. Для объемных входных данных пороговое значение должно быть небольшим. 
* *Символы*: `string` литерал, содержащий список символов, добавляемых в список символов, которые не нарушают термин. (Например, если требуется, `aaa=bbbb` а `aaa:bbb` для каждого — целый термин, а не break в `=` и `:` , используйте `":="` в качестве строкового литерала.)
* *Редуцекинд*: задает параметр сокращения. Единственное допустимое значение времени — `source` .

## <a name="returns"></a>Результаты

Этот оператор возвращает таблицу с тремя столбцами ( `Pattern` , `Count` и `Representative` ) и количеством строк, в которых имеются группы. `Pattern` значение шаблона для группы, `*` используемое в качестве подстановочного знака (представляющий произвольные вставляемые строки), `Count` подсчитывает, сколько строк во входных данных для оператора представлено этим шаблоном, и `Representative` является одним из входных значений, попадающих в эту группу.

Если `[kind=source]` указан параметр, оператор добавит `Pattern` столбец к существующей структуре таблицы.
Обратите внимание, что синтаксис схемы этой разновидности может быть внесен в будущие изменения.

Например, результат выполнения `reduce by city` может быть таким: 

|Модель     |Счетчик |Representative|
|------------|------|--------------|
| Сан *      | 5182 |Бернард сети SAN   |
| Сант *    | 2846 |Сент-Люси    |
| Москва     | 3726 |Москва        |
| \* -ор- \* | 2730 |Один на один  |
| Париж      | 2716 |Париж         |

Еще один пример настройки разметки:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|Модель         |Счетчик|Representative   |
|----------------|-----|-----------------|
|MachineLearning|1000 |MachineLearningX4|

## <a name="examples"></a>Примеры

В следующем примере показано, как можно применить `reduce` оператор к входу, исключенному из-за того, что идентификаторы GUID в столбце, который сокращается, заменяются до сокращения.

```kusto
// Start with a few records from the Trace table.
Trace | take 10000
// We will reduce the Text column which includes random GUIDs.
// As random GUIDs interfere with the reduce operation, replace them all
// by the string "GUID".
| extend Text=replace(@"[[:xdigit:]]{8}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{12}", @"GUID", Text)
// Now perform the reduce. In case there are other "quasi-random" identifiers with embedded '-'
// or '_' characters in them, treat these as non-term-breakers.
| reduce by Text with characters="-_"
```

## <a name="see-also"></a>См. также раздел

[autocluster](./autoclusterplugin.md)

**Примечания**

Реализация оператора в `reduce` основном основана на документе [алгоритм кластеризации данных для шаблонов интеллектуального анализа из журналов событий](https://ristov.github.io/publications/slct-ipom03-web.pdf), Ристо вааранди.
