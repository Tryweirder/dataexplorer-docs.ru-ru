---
title: Передвинуть запрос в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается случайный запрос в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 600e561937b779ff9dd10d5d82f5522d204466a0
ms.sourcegitcommit: 2e63c7c668c8a6200f99f18e39c3677fcba01453
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117698"
---
# <a name="shuffle-query"></a>Передвинуть запрос

Запрос в случайном порядке является преобразованием с сохранением семантики для набора операторов, поддерживающих стратегию случайного перемещения, которая в зависимости от фактических данных может значительно повысить производительность.

Операторы, поддерживающие перетасовывание в Kusto, являются [присоединением](joinoperator.md), [суммированием](summarizeoperator.md) и [созданием рядов](make-seriesoperator.md).

Стратегия запроса в случайном порядке может быть задана `hint.strategy = shuffle` параметром запроса или `hint.shufflekey = <key>`.

Кроме того, можно изучить определение [политики секционирования данных](../management/partitioningpolicy.md) в таблице. Запросы, в которых `shufflekey` также находится ключ хэш-секции таблицы, должны выполняться лучше, так как объем данных, необходимых для перемещения между узлами кластера, значительно уменьшился.

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

Эта стратегия будет предоставлять общую нагрузку на все узлы кластера, где каждый узел будет обрабатывать один раздел данных.
Рекомендуется использовать стратегию запросов случайного использования, если ключ (`join` ключ, `summarize` ключ или `make-series` ключ) имеет большую кратность, что приводит к тому, что обычная стратегия запросов будет обращаться к ограничениям запросов.

**Разница между подсказкой. Стратегия = случайное и указание. шуффлекэй = ключ**

`hint.strategy=shuffle`означает, что оператор, переданный в случайном порядке, будет передаваться по всем ключам.
Например, в этом запросе:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Хэш-функция, которая переведет данные в случайном порядке, будет использовать оба ключа: ActivityId и ProcessId.

Приведенный выше запрос эквивалентен:

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

Это указание можно использовать, если вы заинтересованы в перетасовывание данных всеми ключами оператора, переданного в случайном порядке, так как составной ключ является слишком уникальным, но каждый ключ является неуникальным.
Если оператор, переданный в случайном порядке, `summarize` имеет `join`другие операторы шуффлабле, такие как или, запрос становится более сложным, а затем подсказка. Стратегия = случайная, не будет применена.

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

В этом случае, если мы применяем `hint.strategy=shuffle` (вместо того чтобы пропускать стратегию при планировании запросов) и передвинуть данные в составной ключ [`ActivityId`, `numeric_column`], результат будет неправильным.
Объект `summarize` , расположенный в левой части `join` граубс, подмножеством `join` ключей, которые имеют `ActivityId`значение. Это означает, что `summarize` объект будет группироваться по `ActivityId` ключу, а данные секционированы по составному ключу [`ActivityId`, `numeric_column`].
Перетасовывание по составному ключу [`ActivityId`, `numeric_column`] не означает, что он является допустимым перетасовывание для ключа ActivityId и результаты могут быть неверными.

Этот пример упрощает это, предполагая, что хэш-функция, используемая для составного ключа, — это`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|нумерикколумн|hash_by_key|
|---|---|---|
|Activity1|2|56|
|Activity1|1|65|



Как видите, составной ключ для обеих записей был сопоставлен с различными секциями 56 и 65, но эти две записи имеют одинаковое `ActivityId` значение. Это означает `summarize` , что в левой части, `join` которая ожидает, что аналогичные значения столбца `ActivityId` должны находиться в той же секции, дефинтели выдавать неверные результаты.

В этом случае `hint.shufflekey` решает эту проблему, указывая клавишу "случайное" в соединении `hint.shufflekey = ActivityId` , которая является общим ключом для всех операторов шуффелабле.
В этом случае перетасовывание является надежным, как `join` , так `summarize` и в случайном порядке на один и тот же ключ, поэтому все аналогичные значения будут дефинтели в той же секции, что и результаты верны:

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

|ActivityId|нумерикколумн|hash_by_key|
|---|---|---|
|Activity1|2|56|
|Activity1|1|65|

При выполнении запроса в случайном порядке номер раздела по умолчанию — это номер узла кластера. Это число можно переопределить с помощью синтаксиса `hint.num_partitions = total_partitions` , который будет управлять количеством секций.

Это указание полезно в том случае, если в кластере имеется небольшое количество узлов кластера, где номер раздела по умолчанию будет небольшим, а запрос по-прежнему завершается неудачей или занимает длительное время выполнения.

Обратите внимание, что задание большого количества секций может привести к снижению производительности и потреблению большего количества ресурсов кластера, поэтому рекомендуется тщательно выбирать номер секции (начиная с указания. Стратегия = случайный) и начинает постепенно увеличивать секции.

**Примеры**

В следующем примере показано, как `summarize` в случайном порядке значительно повысить производительность.

Исходная таблица содержит записи 150M, а количество элементов Group By — 10 миллионов, которые распределяются на 10 узлах кластера.

После выполнения обычной `summarize` стратегии запрос завершается после 1:08, а пиковое использование памяти составляет ~ 3 ГБ:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

При использовании стратегии `summarize` случайного использования запрос завершается через ~ 7 секунд, а пиковое использование памяти — 0.43 Гб:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

В следующем примере показано улучшение кластера, в котором есть 2 узла кластера, таблица содержит записи 60 мин, а количество элементов группы по ключу — 2 МБ.

При выполнении запроса без `hint.num_partitions` использования будет использоваться только 2 раздела (номер узла кластера), а следующий запрос займет около 1:10 мин:

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

Если для секций задано значение 10, запрос будет завершен через 23 секунды: 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

В следующем примере показано, как `join` в случайном порядке значительно повысить производительность.

Примеры были выборке в кластере с 10 узлами, где данные распределяются по всем этим узлам.

В левой таблице есть записи 15 млн, в которых количество элементов `join` ключа равно ~ 14M, справа от `join` — с записями 150M, а количество элементов `join` ключа — 10 миллионов.
Выполнив обычную стратегию `join`, запрос заканчивается через ~ 28 секунд, а пиковое использование памяти — 1.43 Гб:

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

При использовании стратегии `join` случайного использования запрос завершается через ~ 4 секунды, а пиковое использование памяти — на 0,3 ГБ:

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Попытка выполнения тех же запросов в наборе данных большего размера, `join` где левая часть — 150M, а кратность ключа — 148M, справа от `join` — 1,5 b, а количество элементов ключа — ~ 100 млн.

Запрос с стратегией по `join` умолчанию обращается к kusto ограничениям и времени ожидания через 4 минуты.
При использовании стратегии `join` случайного использования запрос завершается через ~ 34 секунд, а пиковое использование памяти — 1,23 ГБ.


В следующем примере показано улучшение кластера, в котором есть 2 узла кластера, таблица содержит записи 60 мин, а количество элементов `join` ключа — 2 МБ.
При выполнении запроса без `hint.num_partitions` использования будет использоваться только 2 раздела (номер узла кластера), а следующий запрос займет около 1:10 мин:

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

Если для секций задано значение 10, запрос будет завершен через 23 секунды: 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```
