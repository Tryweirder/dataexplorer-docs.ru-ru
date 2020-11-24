---
title: Оператор WHERE в языке запросов Kusto — Azure обозреватель данных
description: В этой статье описывается оператор WHERE в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6ac800cd4b38396e0f32f44976c4594c093747bb
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512015"
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

## <a name="example-simple-comparisons-first"></a>Пример. сначала простые сравнения

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

В этом примере извлекаются записи, которые не старше 1 часа, берутся из источника с именем `MyCluster` и имеют два столбца с одинаковым значением. 

Обратите внимание, что сравнение двух столбцов выполняется последним, так как оно не может использовать индекс и вызывает проверку.

## <a name="example-columns-contain-string"></a>Пример: столбцы содержат строку

```kusto
Traces | where * has "Kusto"
```

Все строки, в которых слово «Kusto» отображается в любом столбце.
 
