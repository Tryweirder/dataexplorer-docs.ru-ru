---
title: Оператор where языка запросов Kusto в Azure Data Explorer
description: В этой статье описывается оператор where в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6ac800cd4b38396e0f32f44976c4594c093747bb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512015"
---
# <a name="where-operator"></a>Оператор where

Отфильтровывает таблицу для подмножества строк, которые удовлетворяют предикату.

```kusto
T | where fruit=="apple"
```

**Псевдоним** `filter`

## <a name="syntax"></a>Синтаксис

*T* `| where`*предикат*

## <a name="arguments"></a>Аргументы

* *T*. Табличные входные данные, записи которых будут отфильтрованы.
* *Предикат*. Выражение `boolean` [](./scalar-data-types/bool.md) для столбцов *T*. Вычисляется отдельно для каждой строки в *T*.

## <a name="returns"></a>Возвращаемое значение

Строки из таблицы *T*, для которых *Predicate* имеет значение `true`.

**Примечания.** Значения NULL. При сравнении со значениями NULL все функции фильтрации возвращают значение false. Для написания запросов, обрабатывающих значения NULL, можно использовать специальные функции, поддерживающие значение NULL.

[isnull()](./isnullfunction.md), [isnotnull()](./isnotnullfunction.md), [isempty()](./isemptyfunction.md), [isnotempty()](./isnotemptyfunction.md). 

**Советы**

Обеспечить максимальную производительность можно так.

* **Используйте простые сравнения** между именами столбцов и константами. (Под константой здесь понимается значение, постоянное для всех строк таблицы. Соответственно, допустимо использовать функции `now()` и `ago()`, а также скалярные значения, назначенные с помощью [оператора `let`](./letstatement.md).)

    Например, лучше использовать `where Timestamp >= ago(1d)`, чем `where floor(Timestamp, 1d) == ago(1d)`.

* **Вводите сначала более простые элементы**. Если у вас есть несколько предложений, объединенных с помощью `and`, то сначала вводите предложения, которые включают только один столбец. Пример правильного порядка предложений: `Timestamp > ago(1d) and OpId == EventId`.

Дополнительные сведения см. в сводке [доступных строковых операторов](./datatypes-string-operators.md) и в сводке [доступных числовых операторов](./numoperators.md).

## <a name="example-simple-comparisons-first"></a>Пример Сначала простые сравнения

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

В этом примере извлекаются записи не старше 1 часа из источника с именем `MyCluster`, которые имеют два столбца с одинаковым значением. 

Обратите внимание: сравнение двух столбцов вводится в последнюю очередь, так как эта операция не предполагает использование индекса и запуска проверки.

## <a name="example-columns-contain-string"></a>Пример Столбцы содержат строку

```kusto
Traces | where * has "Kusto"
```

Все строки, в которых в любом столбце отображается слово "Kusto".
 
