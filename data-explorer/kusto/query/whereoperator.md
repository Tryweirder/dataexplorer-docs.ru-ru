---
title: Оператор WHERE в языке запросов Kusto — Azure обозреватель данных
description: В этой статье описывается оператор WHERE в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ed207a9db0b6440bae8f8fb4ae1c250d274565d7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338224"
---
# <a name="where-operator"></a>Оператор where

Отфильтровывает таблицу для подмножества строк, которые удовлетворяют предикату.

```kusto
T | where fruit=="apple"
```

**Псевдоним**`filter`

## <a name="syntax"></a>Синтаксис

*T* `| where` *предикат*

## <a name="arguments"></a>Аргументы

* *T*: табличный ввод, записи которого должны быть отфильтрованы.
* *Predicate*— `boolean` [выражение](./scalar-data-types/bool.md) для столбцов *T*. Он вычисляется для каждой строки в *T*.

## <a name="returns"></a>Возвращаемое значение

Строки из таблицы *T*, для которых *Predicate* имеет значение `true`.

**Примечания** Значения NULL. при сравнении со значениями null все функции фильтрации возвращают значение false. Для написания запросов, обрабатывающих значения NULL, можно использовать специальные функции, поддерживающие значение null.

[IsNull ()](./isnullfunction.md), [isnotnull ()](./isnotnullfunction.md), [IsEmpty ()](./isemptyfunction.md), [иснотемпти ()](./isnotemptyfunction.md). 

**Советы**

Обеспечить максимальную производительность можно так.

* **Используйте простые сравнения** между именами столбцов и константами. ("Константа" означает константу для таблицы, `now()` поэтому `ago()` они и являются нормальными и поэтому являются скалярными значениями, назначенными с помощью [ `let` оператора](./letstatement.md).)

    Например, лучше использовать `where Timestamp >= ago(1d)`, чем `where floor(Timestamp, 1d) == ago(1d)`.

* **Вводите сначала более простые элементы**. Если у вас есть несколько предложений, объединенных с помощью `and`, то сначала вводите предложения, которые включают только один столбец. Пример правильного порядка предложений: `Timestamp > ago(1d) and OpId == EventId`.

Дополнительные сведения см. в сводке [доступных строковых операторов](./datatypes-string-operators.md) и сводных данных о [доступных числовых операторах](./numoperators.md).

## <a name="example"></a>Пример

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

В этом примере извлекаются записи, которые не старше 1 часа, берутся из источника с именем `MyCluster` и имеют два столбца с одинаковым значением. 

Обратите внимание, что сравнение двух столбцов выполняется последним, так как оно не может использовать индекс и вызывает проверку.

## <a name="example"></a>Пример

```kusto
Traces | where * has "Kusto"
```

Все строки, в которых слово «Kusto» отображается в любом столбце.
 
