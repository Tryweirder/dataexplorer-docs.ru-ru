---
title: Перемешать запрос - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается запрос Shuffle в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c687d495a41a5f73ac8dbca15d93729f2132a556
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662974"
---
# <a name="shuffle-query"></a>Перемешать запрос

Shuffle-запрос — это семантическое сохранение преобразования для набора операторов, которое поддерживает стратегию перетасовки, которая в зависимости от фактических данных может дать значительно более высокую производительность.

Операторы, поддерживающие перетасовку в Кусто, [присоединяются,](joinoperator.md) [суммируют](summarizeoperator.md) и [составляют серию.](make-seriesoperator.md)

Стратегия shuffle запроса может быть установлена `hint.strategy = shuffle` `hint.shufflekey = <key>`параметром запроса или .

**Синтаксис**

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

```kusto
T
| summarize hint.strategy = shuffle count(), avg(price) by supplier
```

```kusto
T
| make-series hint.shufflekey = Fruit PriceAvg=avg(Price) default=0  on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

Эта стратегия будет разделять нагрузку на все кластерные узлы, где каждый узла будет обрабатывать один раздел данных.
Полезно использовать стратегию перетасовки запроса, `summarize` когда `make-series` ключ (ключ,`join` ключ или ключ) имеет высокую кардинальность, вызывая регулярную стратегию запроса, чтобы достичь пределов запроса.

**Разница между hint.strategy-shuffle и hint.shufflekey**

`hint.strategy=shuffle`означает, что перетасованная оператор будет перетасована всеми ключами.
Например, в этом запросе:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Функция хэша, которая перетасовывает данные, будет использовать оба ключа ActivityId и ProcessId.

Приведенный выше запрос эквивалентен:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Этот намек может быть использован, когда вы заинтересованы в перетасовке данных всеми клавишами перетасованы оператора, потому что соединение ключ слишком уникален, но каждый ключ не является достаточно уникальным.
Когда у перетасованный оператор имеет `summarize` другие перемешиваемые операторы, такие как или, `join`запрос становится более сложным, а затем hint.strategy-shuffle не будет применяться.

Например:

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.strategy = shuffle (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

В этом случае, если `hint.strategy=shuffle` мы применяем (вместо игнорирования стратегии во время планирования`ActivityId` `numeric_column`запроса) и перетасуем данные по ключам соединения, результат не будет правильным.
Который `summarize` находится на левой стороне `join` groubs по подмножеством ключей, `join` который . `ActivityId` Это означает, `summarize` что воля `ActivityId` группируется по ключу, в`ActivityId`то `numeric_column`время как данные разделены клавишой соединения,
Перетасовка по ключам `numeric_column`соединения,`ActivityId`не означает, что это допустимое перетасовка для ключа ActivityId и результаты могут быть неверными.

Этот пример упрощает это, предполагая, что функция хэша, используемая для соединения ключа`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|Число в колонне|hash_by_key|
|---|---|---|
|активность1|2|56|
|активность1|1|65|



Как вы видите соединение ключ для обеих записей был отображен на различных `ActivityId` разделов 56 и 65, но эти две записи имеет то же значение, которое `summarize` означает, что на левой стороне, `join` которая ожидает аналогичные значения столбца `ActivityId` будет в том же разделе будет defintely производить неправильные результаты.

В этом `hint.shufflekey` случае, решает эту проблему, указав тасовку ключ на соединение, к `hint.shufflekey = ActivityId` которому является общим ключом для всех shuffelable операторов.
В этом случае перетасовка безопасна, как `join` и `summarize` перетасовывается тем же ключом, так что все аналогичные значения будут декламировать быть в одном и том же разделе результаты верны:

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.shufflekey = ActivityId (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

|ActivityId|Число в колонне|hash_by_key|
|---|---|---|
|активность1|2|56|
|активность1|1|65|

В запросе перетасовки число перегородок является числом кластерных узлов. Это число можно переопределить с помощью синтаксиса, `hint.num_partitions = total_partitions` который будет контролировать количество разделов.

Этот подсказка полезна, когда кластер имеет небольшое количество кластерных узлов, где число разделов по умолчанию тоже будет небольшим, а запрос по-прежнему выходит из строя или занимает длительное время выполнения.

Обратите внимание, что установка многих разделов может ухудшить производительность и потреблять больше кластерных ресурсов, поэтому рекомендуется тщательно выбирать число разделов (начиная с hint.strategy - перетасовывать и постепенно увеличивать разделы).

**Примеры**

Следующий пример показывает, как перетасовка `summarize` значительно повышает производительность.

Таблица исходных источников имеет 150M записи и кардинальность группы по ключу составляет 10M, которая распространяется на 10 кластерных узлов.

Запуск обычной `summarize` стратегии, запрос заканчивается после 1:08, а пик использования памяти составляет 3 ГБ:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

При использовании стратегии перетасовки `summarize` запрос заканчивается через 7 секунд, а пик использования памяти составляет 0,43 ГБ:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

Следующий пример показывает улучшение кластера, который имеет 2 кластерных узла, таблица имеет 60M записи и кардинальность группы по ключу 2M.

Запуск запроса `hint.num_partitions` без использования только 2 разделов (в качестве числа кластерных узлов) и следующий запрос займет 1:10 минут:

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

установка числа разделов до 10, запрос закончится через 23 секунды: 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

Следующий пример показывает, как перетасовка `join` значительно повышает производительность.

Примеры были отобраны в кластере с 10 узлами, где данные распространяются по всем этим узлам.

Левая таблица имеет 15M записи, где кардинальность `join` ключа составляет 14M, правая сторона `join` с 150M записей и кардинальность `join` ключа составляет 10M.
Запуск обычной стратегии `join`запроса заканчивается через 28 секунд, а пик использования памяти составляет 1,43 ГБ:

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

При использовании стратегии перетасовки `join` запрос заканчивается через 4 секунды, а пик использования памяти составляет 0,3 ГБ:

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Попытка же запросы на больший набор данных, где левая `join` сторона 150M и кардинальность ключа `join` 148M, правая сторона 1.5B и кардинальность ключа составляет 100M.

Запрос со стратегией `join` по умолчанию достигает пределов и разовых сроков после 4 минут.
При использовании стратегии перетасовки `join` запрос заканчивается через 34 секунды, а пик использования памяти составляет 1,23 ГБ.


Следующий пример показывает улучшение кластера, который имеет 2 кластерных узла, таблица `join` имеет 60M записи и кардинальность ключа составляет 2M.
Запуск запроса `hint.num_partitions` без использования только 2 разделов (в качестве числа кластерных узлов) и следующий запрос займет 1:10 минут:

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

установка числа разделов до 10, запрос закончится через 23 секунды: 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```