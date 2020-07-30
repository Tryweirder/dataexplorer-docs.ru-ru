---
title: Запрос в случайном порядке — Azure обозреватель данных
description: В этой статье описывается случайный запрос в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e011ffa61b70c79d51941518de0624030d847c4e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351103"
---
# <a name="shuffle-query"></a>Запрос на перетасовку

Запрос в случайном порядке является преобразованием с сохранением семантики для набора операторов, поддерживающих стратегию случайного перемещения. В зависимости от фактических данных этот запрос может значительно повысить производительность.

Операторы, поддерживающие перетасовывание в Kusto, — это [объединение](joinoperator.md), [суммирование](summarizeoperator.md)и [создание рядов](make-seriesoperator.md).

Настройте стратегию запросов в случайном порядке с помощью параметра запроса `hint.strategy = shuffle` или `hint.shufflekey = <key>` .

Определите [политику секционирования данных](../management/partitioningpolicy.md) для таблицы. 

Задайте в `shufflekey` качестве ключа хэш-секции таблицы для повышения производительности, так как объем данных, необходимых для перемещения между узлами кластера, уменьшается.

## <a name="syntax"></a>Синтаксис

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
Рекомендуется использовать стратегию запросов случайного использования, когда ключ ( `join` ключ, `summarize` ключ или `make-series` ключ) имеет большую кратность, а обычная стратегия запросов достигает ограничений запросов.

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

Если составной ключ является слишком уникальным, но каждый ключ не является уникальным, используйте его `hint` для пересчета данных всеми ключами оператора, переданного в случайном порядке.
Если у оператора, переданного в случайном порядке, есть другие операторы, такие как `summarize` или `join` , запрос станет более сложным, а затем подсказка. Стратегия = случайное перемещение не будет применена.

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

Если применить `hint.strategy=shuffle` (вместо того чтобы пропускать стратегию во время планирования запросов) и передвинуть данные по составному ключу [ `ActivityId` , `numeric_column` ], результат будет неправильным.
`summarize`Оператор находится в левой части `join` оператора. Этот оператор будет группировать по подмножеству `join` ключей, которое в нашем случае имеет значение `ActivityId` . Таким `summarize` же блок будет группироваться по ключу `ActivityId` , а данные будут секционированы по составному ключу [ `ActivityId` , `numeric_column` ].
Перетасовывание по составному ключу [ `ActivityId` , `numeric_column` ] не обязательно означает, что перетасовывание для ключа `ActivityId` является допустимым, и результаты могут быть неверными.

В этом примере предполагается, что хэш-функция, используемая для составного ключа `binary_xor(hash(key1, 100) , hash(key2, 100))` :

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

Составной ключ для обеих записей был сопоставлен с разными секциями (56 и 65), но эти две записи имеют одинаковое значение `ActivityId` . `summarize`Оператор в левой части объекта `join` принимает аналогичные значения столбца в `ActivityId` одну и ту же секцию. При выполнении этого запроса будут получены неверные результаты.

Эту проблему можно решить, используя `hint.shufflekey` , чтобы указать клавишу "случайный" в соединении с `hint.shufflekey = ActivityId` . Этот ключ является общим для всех операторов, поддерживающих случайное перемещение.
В этом случае перетасовывание является надежным, так как `join` и, и `summarize` в случайном порядке с одним и тем же ключом. Таким образом, все аналогичные значения будут находиться в одной секции, и результаты будут верными:

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

> [!Note]
> Наличие большого количества разделов может потреблять больше ресурсов кластера и снижает производительность. Вместо этого следует тщательно выбрать номер секции, начиная с указания. Стратегия = случайный, и начать увеличивать секции постепенно.

## <a name="examples"></a>Примеры

В следующем примере показано, как в случайном порядке `summarize` значительно повысить производительность.

Исходная таблица содержит записи 150M, а количество элементов Group By — 10 миллионов, которое распространяется на 10 узлов кластера.

После выполнения обычной `summarize` стратегии запрос завершается после 1:08, а пиковое использование памяти — на 3 ГБ:

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Счетчик|
|---|
|1086|

При использовании стратегии случайного использования `summarize` запрос завершается через ~ 7 секунд, а пиковое использование памяти составляет 0,43 Гб:

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Счетчик|
|---|
|1086|

В следующем примере показано улучшение кластера с двумя узлами кластера, таблица содержит записи 60 мин, а количество элементов группы по ключу — 2 МБ.

При выполнении запроса без `hint.num_partitions` использования будут использоваться только два раздела (номер узла кластера), и следующий запрос займет около 1:10 мин:

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

В следующем примере показано, как в случайном порядке `join` значительно повысить производительность.

Примеры были выборке в кластере с 10 узлами, где данные распределяются по всем этим узлам.

В левой таблице есть записи 15 млн, в которых количество элементов `join` ключа равно ~ 14M. В правой части объекта `join` есть записи 150M, а количество элементов `join` ключа — 10 миллионов.
Выполнив обычную стратегию `join` , запрос заканчивается через ~ 28 секунд, а пиковое использование памяти — 1,43 Гб:

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

При использовании стратегии случайного использования `join` запрос завершается через ~ 4 секунды, а пиковое использование памяти составляет 0,3 ГБ:

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

Попытка выполнения тех же запросов в наборе данных большего размера, где левая часть `join` — 150M, а кратность ключа — 148M. Правая часть `join` — 1,5 б, а количество элементов ключа — ~ 100 млн.

Запрос с стратегией по умолчанию обращается к `join` Kusto ограничениям и времени ожидания через 4 минуты.
При использовании стратегии случайного использования `join` запрос завершается через ~ 34 секунд, а пиковое использование памяти — на 1,23 ГБ.


В следующем примере показано улучшение кластера с двумя узлами кластера, таблица содержит записи 60 мин, а количество элементов `join` ключа — 2 МБ.
При выполнении запроса без `hint.num_partitions` использования будут использоваться только два раздела (номер узла кластера), и следующий запрос займет около 1:10 мин:

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
