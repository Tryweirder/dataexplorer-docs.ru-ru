---
title: Оператор join в Azure Data Explorer
description: В этой статье описывается оператор join в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b90e5f1c95ec75a946490cd75b5dd89ad2cb1aba
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513341"
---
# <a name="join-operator"></a>Оператор join

Узнайте, как объединить строки двух таблиц для формирования новой таблицы путем сопоставления значений указанных столбцов каждой таблицы.

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>Синтаксис

*LeftTable* `|` `join` [*JoinParameters*] `(` *RightTable* `)` `on` *Attributes*

## <a name="arguments"></a>Аргументы

* *LeftTable* — **левое** табличное выражение или таблица, иногда называемая **внешней** таблицей, строки которой нужно объединить. Обозначается как `$left`.

* *RightTable* — **правое** табличное выражение или таблица, иногда называемая **внутренней** таблицей, строки которой нужно объединить. Обозначается как `$right`.

* *Attributes* — одно или несколько разделенных запятыми **правил**, которые описывают, как строки из *LeftTable* сопоставляются со строками *RightTable*. Несколько правил оцениваются с помощью логического оператора `and`.

  **Правило** может иметь один из следующих типов.

  |Тип правила        |Синтаксис          |Predicate    |
  |-----------------|--------------|-------------------------|
  |Равенство по имени |*ColumnName*    |`where` *LeftTable*.*ColumnName* `==` *RightTable*.*ColumnName*|
  |Равенство по значению|`$left.`*LeftColumn* `==` `$right.`*RightColumn*|`where` `$left.`*LeftColumn* `==` `$right.`*RightColumn*       |

    > [!NOTE]
    > Для равенства по значению имена столбцов *должны* быть обозначены нотациями `$left` и `$right` по применимой таблице.

* *JoinParameters* — ноль или более разделенных пробелами параметров в виде *имя* `=` *значение*, управляющих поведением операции сопоставления строк и планом выполнения. Поддерживаются следующие параметры:

    ::: zone pivot="azuredataexplorer"

    |Имя параметра           |Значения                                        |Описание                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Разновидности оператора соединения|См. раздел [Варианты оператора join](#join-flavors).|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |См. статью [Межкластерный оператор join](joincrosscluster.md).|
    |`hint.strategy`|Указания по выполнению                               |См. раздел [Указания для оператора join](#join-hints).                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |Имя           |Значения                                        |Описание                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |Разновидности оператора соединения|См. раздел [Варианты оператора join](#join-flavors).|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|Указания по выполнению                               |См. раздел [Указания для оператора join](#join-hints).                |

    ::: zone-end

> [!WARNING]
> Если тип `kind` не указан, по умолчанию для оператора join выбирается вариант `innerunique`. Такой подход отличается от подхода в некоторых других продуктах аналитики, которые используют `inner` в качестве варианта по умолчанию.  Чтобы понять различия и гарантировать получение нужных результатов, см. раздел [Варианты оператора join](#join-flavors).

## <a name="returns"></a>Возвращаемое значение

**Схема вывода зависит от варианта оператора join:**

| Вариант оператора join | Схема вывода |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| Итоговая таблица содержит только столбцы из левой части.|
| `kind=rightanti`, `kind=rightsemi` | Итоговая таблица содержит только столбцы из правой части.|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  Столбец для каждого столбца в каждой из двух таблиц, в том числе соответствующие ключи. В случае конфликта имен столбцы в правой части будут автоматически переименованы. |
   
**Выходные записи зависят от варианта оператора join:**

   > [!NOTE]
   > Если есть несколько строк с одинаковыми значениями для этих полей, вы получите строки для всех комбинаций.
   > Соответствие — это строки из обеих таблиц, у которых совпадают значения всех полей `on` .

| Вариант оператора join | Выходные записи |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| Возвращает все записи из левой части, для которых нет соответствий в правой.|
| `kind=rightanti`, `kind=rightantisemi`| Возвращает все записи из правой части, для которых нет соответствий в левой.|
| `kind` не указан, `kind=innerunique`| Каждому значению ключа `on` соответствует только одна строка из левой части. Выходные данные содержат по одной строке для каждого соответствия этой строки со строками из правой части.|
| `kind=leftsemi`| Возвращает все записи из левой части с соответствиями в правой. |
| `kind=rightsemi`| Возвращает все записи из правой части с соответствиями в левой. |
|`kind=inner`| Содержит строку в выходных данных для каждого сочетания соответствующих строк из левой и правой частей. |
| `kind=leftouter` (или `kind=rightouter`, или `kind=fullouter`)| Содержит по одной строке для каждой строки в левой и правой частях, даже если совпадения отсутствуют. Выходные ячейки без сопоставлений имеют значения NULL. |

> [!TIP]
> Если одна таблица меньше другой, для оптимальной производительности используйте ее в качестве левой (перенаправленной) части оператора join.

## <a name="example"></a>Пример

Получите расширенные сведения о действиях из `login`, которым некоторые записи помечают время начала и конца действия.

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityId, StartTime=timestamp
| join (Events
    | where Name == "Stop"
        | project StopTime=timestamp, ActivityId)
    on ActivityId
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityIdLeft = ActivityId, StartTime=timestamp
| join (Events
        | where Name == "Stop"
        | project StopTime=timestamp, ActivityIdRight = ActivityId)
    on $left.ActivityIdLeft == $right.ActivityIdRight
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

## <a name="join-flavors"></a>Разновидности оператора соединения

Точный вариант оператора join указывается с помощью ключевого слова *kind*. Поддерживаются следующие варианты оператора join:

|Тип и вариант оператора join|Описание|
|--|--|
|[`innerunique`](#default-join-flavor) (или пустое значение по умолчанию)|Внутреннее соединение с удалением дубликатов в левой части|
|[`inner`](#inner-join-flavor)|Стандартное внутреннее соединение|
|[`leftouter`](#left-outer-join-flavor)|левое внешнее соединение.|
|[`rightouter`](#right-outer-join-flavor)|Правое внешнее соединение|
|[`fullouter`](#full-outer-join-flavor)|Полное внешнее соединение|
|[`leftanti`](#left-anti-join-flavor), [`anti`](#left-anti-join-flavor) или [`leftantisemi`](#left-anti-join-flavor)|Левое антисоединение|
|[`rightanti`](#right-anti-join-flavor) или [`rightantisemi`](#right-anti-join-flavor)|Правое антисоединение|
|[`leftsemi`](#left-semi-join-flavor)|Левое полусоединение|
|[`rightsemi`](#right-semi-join-flavor)|Правое полусоединение|

### <a name="default-join-flavor"></a>Вариант оператора join по умолчанию

Вариантом оператора join по умолчанию является внутреннее соединение с удалением дубликатов в левой части. Реализация оператора join по умолчанию полезна в типичных сценариях анализа журналирования/трассировки, где необходимо коррелировать два события, каждое из которых соответствует определенному критерию фильтрации, с одним идентификатором корреляции. При этом не обращайте внимания на видимые аспекты и игнорируйте появление записей трассировки.

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

Чтобы объяснить операцию соединения с помощью оператора join, ниже приведены два примера таблиц.

**Таблица X**

|Ключ |Значение1
|---|---
|a |1
|b |2
|b |3
|с |4

**Таблица Y**

|Ключ |Значение2
|---|---
|b |10
|с |20
|с |30
|d |40

Соединение по умолчанию выполняет внутреннее соединение после удаления дубликатов в левой части по ключу соединения (первая запись при дедупликации сохраняется).

Рассмотрим эту инструкцию: `X | join Y on Key`.

Эффективная левая сторона оператора join (таблица X после удаления дубликатов) будет следующей:

|Ключ |Значение1
|---|---
|a |1
|b |2
|с |4

а в результате объединения мы получим следующее:

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join Y on Key
```

|Ключ|Значение1|Key1|Значение2|
|---|---|---|---|
|b|2|b|10|
|с|4|с|20|
|с|4|с|30|

> [!NOTE]
> Ключи a и d не отображаются в выходных данных, поскольку соответствующие ключи отсутствуют и справа, и слева.

### <a name="inner-join-flavor"></a>Вариант inner-join

Функция inner-join похожа на стандартную функцию inner-join в SQL. Выходная запись создается каждый раз, когда у записи с левой стороны есть тот же ключ для соединения, что и у записи с правой стороны.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=inner Y on Key
```

|Ключ|Значение1|Key1|Значение2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|с|4|с|20|
|с|4|с|30|

> [!NOTE]
> * (b,10) с правой стороны соединено дважды: с (b,2) и с (b,3) слева.
> * (c,4) с левой стороны соединено дважды: с (c,20) и с (c,30) справа.

### <a name="innerunique-join-flavor"></a>Вариант innerunique-join
 
Используйте **вариант innerunique-join** для удаления дубликатов ключей с левой стороны. Результат будет представлять собой строку в выходных данных каждого сочетания ключей слева и справа с удаленными дубликатами.

> [!NOTE]
> **innerunique** может приводить к получению двух возможных вариантов выходных данных, каждый из которых будет правильным.
В первых выходных данных оператор join случайным образом выбрал первый ключ, который отображается в t1 со значением val1.1, и сопоставил его с ключами t2.
Во вторых выходных данных оператор join случайным образом выбрал второй ключ, который отображается в t1 со значением val1.2, и сопоставил его с ключами t2.

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3",
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|ключ|value|key1|value1|
|---|---|---|---|
|1|val1.1|1|val1.3|
|1|val1.1|1|val1.4|

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|ключ|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

* Решение Kusto оптимизировано для смещения фильтров (при возможности), которые указаны после `join`, ближе к соответствующей стороне join (левой или правой).

* В некоторых случаях используется вариант **innerunique**, а фильтр распространяется на левую сторону оператора join. Этот вариант будет распространяться автоматически, а ключи, применяемые к этому фильтру, всегда будут отображаться в выходных данных.
    
* Используйте приведенный выше пример и добавьте фильтр `where value == "val1.2" `. Он всегда будет давать второй результат и никогда не будет давать первый для наборов данных:

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
| where value == "val1.2"
```

|ключ|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

### <a name="left-outer-join-flavor"></a>Вариант left outer-join

Результат левого внешнего соединения для таблиц X и Y всегда содержит все записи из левой таблицы (X), даже если условие соединения не соответствует ни одной из записей из правой таблицы (Y).

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftouter Y on Key
```

|Ключ|Значение1|Key1|Значение2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|с|4|с|20|
|с|4|с|30|
|a|1|||

### <a name="right-outer-join-flavor"></a>Вариант right outer-join

Вариант для правого внешнего соединения напоминает левое внешнее соединение, но обработка таблиц осуществляется в обратном порядке.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightouter Y on Key
```

|Ключ|Значение1|Key1|Значение2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|с|4|с|20|
|с|4|с|30|
|||d|40|

### <a name="full-outer-join-flavor"></a>Вариант full outer-join

Полное внешнее соединение сочетает в себе действие левого и правого внешних соединений. В тех случаях, когда записи в соединяемых таблицах не совпадают, результирующий набор будет иметь значения `null` для каждого столбца таблицы, в котором нет совпадающей строки. Для тех записей, для которых соответствия нет, в результирующий набор будет добавлена одна строка (содержащая поля, заполненные из обеих таблиц).

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=fullouter Y on Key
```

|Ключ|Значение1|Key1|Значение2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|с|4|с|20|
|с|4|с|30|
|||d|40|
|a|1|||

### <a name="left-anti-join-flavor"></a>Вариант left anti-join

Левое антисоединение возвращает все записи с левой стороны, которые не соответствуют ни одной записи с правой стороны.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftanti Y on Key
```

|Ключ|Значение1|
|---|---|
|a|1|

> [!NOTE]
> Антисоединение моделирует запрос NOT IN.

### <a name="right-anti-join-flavor"></a>Вариант right anti-join

Правое антисоединение возвращает все записи с правой стороны, которые не соответствуют ни одной записи с левой стороны.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightanti Y on Key
```

|Ключ|Значение2|
|---|---|
|d|40|

> [!NOTE]
> Антисоединение моделирует запрос NOT IN.

### <a name="left-semi-join-flavor"></a>Вариант left semi-join

Левое полусоединение возвращает все записи с левой стороны, которые соответствуют записи с правой стороны. Возвращаются только столбцы из левой части.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftsemi Y on Key
```

|Ключ|Значение1|
|---|---|
|b|3|
|b|2|
|с|4|

### <a name="right-semi-join-flavor"></a>Вариант right semi-join

Правое полусоединение возвращает все записи с правой стороны, которые соответствуют записи с левой стороны. Возвращаются только столбцы из правой части.

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightsemi Y on Key
```

|Ключ|Значение2|
|---|---|
|b|10|
|с|20|
|с|30|

### <a name="cross-join"></a>Перекрестное соединение

Kusto изначально не предоставляет вариант cross-join. Вы не можете отметить оператор как `kind=cross`.
Для имитации используйте фиктивный ключ.

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>Указания для оператора join

Оператор `join` поддерживает несколько указаний, управляющих способом выполнения запроса.
Такие указания не изменяют семантику оператора `join`, но могут повлиять на его производительность.

Указания для оператора join описаны в следующих статьях:

* `hint.shufflekey=<key>` и `hint.strategy=shuffle` - [запрос на перетасовку](shufflequery.md)
* `hint.strategy=broadcast` - [всенаправленное соединение](broadcastjoin.md)
* `hint.remote=<strategy>` - [соединение между кластерами](joincrosscluster.md)
