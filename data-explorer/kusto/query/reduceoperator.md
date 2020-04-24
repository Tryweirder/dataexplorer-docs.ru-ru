---
title: сокращение оператора - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается оператор сокращения в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33d4ac202b61fdaa1b92291407cdd2783d947c6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510512"
---
# <a name="reduce-operator"></a>Оператор reduce

Группирует набор строк вместе на основе сходства значений.

```kusto
T | reduce by LogMessage with threshold=0.1
```

Для каждой такой группы он выводит **шаблон,** который лучше всего`*`описывает группу (возможно, используя символ asterix () для представления подстановочных знаков), **подсчет** количества значений в группе и **представителя** группы (одно из исходных значений в группе).

**Синтаксис**

*T* `|` `threshold` `=` `with` `,` `characters` `=` *Characters* *ReduceKind* `by` *Expr* *Threshold*-`kind` `=` ReduceKind , Expr , Пороговый порог - символы `reduce`

**Аргументы**

* *Expr*: Выражение, которое `string` оценивает значение.
* *Порог*: `real` буквальный в диапазоне (0..1). По умолчанию 0,1. Для объемных входных данных пороговое значение должно быть небольшим. 
* *Символы*: `string` Буквальное, содержащее список символов, чтобы добавить в список символов, которые не нарушают термин. (Например, если `aaa=bbbb` вы `aaa:bbb` хотите, и каждый из `=` них `:`весь `":="` срок, а не перерыв на и , использовать в качестве строки буквально.)
* *ReduceKind*: Определяет уменьшить вкус. Единственным действительным значением на `source`время является .

**Возвращает**

Этот оператор возвращает таблицу с`Pattern` `Count`тремя `Representative`столбиками (, и ), и , и столько строк, сколько есть группы. `Pattern`значение шаблона для группы, с `*` используется в качестве подстановочного знака (представляющий произвольные строки вставки), `Count` подсчитывает, сколько строк в входе оператора, представленного этим шаблоном, и `Representative` является одним значением от входных данных, которые попадают в эту группу.

Если `[kind=source]` указано, оператор придаст `Pattern` столбец существующей структуре таблицы.
Обратите внимание, что синтаксис схемы этого вкуса могут быть подвергнуты будущим изменениям.

Например, результат выполнения `reduce by city` может быть таким: 

|Модель     |Count |Representative|
|------------|------|--------------|
| Сан *      | 5182 |Сан-Бернар   |
| Сант *    | 2846 |Сэйнт Люси    |
| Москва     | 3726 |Москва        |
| \* -ор- \* | 2730 |Один -на-один  |
| Париж      | 2716 |Париж         |

Другой пример с настраиваемым токенизацией:

```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|Модель         |Count|Representative   |
|----------------|-----|-----------------|
|Машинное обучение|1000 |Машинное обучениеX4|

**Примеры**

Ниже приводится следующий пример, как можно применить `reduce` оператора к "дезинфицированному" входному ввозам, при котором GUIDs в столбце заменяются до сокращения

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

**См. также:**

[autocluster](./autoclusterplugin.md)

**Примечания**

Реализация `reduce` оператора в значительной степени основана на документе [Алгоритм кластеризации данных для анализа моделей майнинга из журналов событий,](https://ristov.github.io/publications/slct-ipom03-web.pdf)Ристо Вааранди.