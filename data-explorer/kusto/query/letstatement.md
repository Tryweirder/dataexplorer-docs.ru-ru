---
title: Оператор let — Azure Data Explorer
description: Узнайте, как использовать оператор let в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 7723a3ccd06963596744db9c73f499a882b54c3e
ms.sourcegitcommit: 1530a38181ec92ed1c2c1f3aa2a75f69bd3e9045
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822786"
---
# <a name="let-statement"></a>Инструкция let

Операторы let выполняют привязку имен к выражениям. До конца области, в которой появляется оператор let, имя может использоваться для создания ссылки на его связанное значение. Оператор let можно указать в глобальной области или области тела функции.
Если такое имя ранее было привязано к другому значению, используется привязка innermost оператора let.

Операторы let повышают модульность и расширяют возможности повторного использования, так как позволяют разбивать потенциально сложное выражение на несколько частей.
Каждая часть привязывается к имени с помощью оператора let, и вместе они составляют цельный объект. Они также могут использоваться для создания определяемых пользователем функций и представлений. Представления являются выражениями по таблицам, результаты которых выглядят как новая таблица.

> [!NOTE]
> Имена, связываемые операторами let, должны быть допустимыми именами сущностей.

Выражения, связываемые операторами let, могут иметь следующий тип:
* Скалярные типы
* табличные типы;
* определяемые пользователем функции (лямбда-функции).

## <a name="syntax"></a>Синтаксис

`let` *Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

|Поле  |Определение  |Пример  |
|---------|---------|---------|
|*имя*;   | Привязываемое имя. Имя должно быть допустимым именем сущности.    |Допускается экранирование имен сущностей, например `["Name with spaces"]`.      |
|*ScalarExpression*     |  Выражение со скалярным результатом, значение которого привязывается к имени.  | `let one=1;`  |
|*TabularExpression*    | Выражение с табличным результатом, значение которого привязывается к имени.   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | Выражение, результатом которого является лямбда-функция — анонимное объявление функции, которое привязывается к имени.   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>Синтаксис лямбда-выражений

[`view`] `(`[*TabularArguments*][`,`][*ScalarArguments*]`)` `{` *FunctionBody* `}`

`TabularArguments` - [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ]`)`] [`,` ... ][`,`]

 или

 [*TabularArgName* `:` `(` `*` `)`] — указывает на табличные выражения с переменными и неизвестными столбцами.

`ScalarArguments` - [*ArgName* `:` *ArgType*] [`,` ... ]


|Поле  |Определение  |Пример  |
|---------|---------|---------|
| **view** | Может использоваться только в инструкции let без параметров, не имеющей аргументов. Если используется ключевое слово View, инструкция let будет включена в запросы, в которых используется оператор `union` с выбором подстановочных знаков для таблиц и представлений. |  |
| **_TabularArguments_* _ | Список аргументов формального табличного выражения. 
| Каждый табличный аргумент включает следующее:||
|<ul><li> _TabularArgName*</li></ul> | Имя формального табличного аргумента. Это имя может использоваться в *FunctionBody*. Оно привязано к определенному значению при вызове лямбда-функции. ||
|<ul><li>Определение схемы таблицы </li></ul> | Список атрибутов с их типами| AtrName : AtrType|
| ***ScalarArguments** _ | Список формальных скалярных аргументов. 
|Каждый скалярный аргумент включает следующее:||
|<ul><li>_ArgName*</li></ul> | Имя формального скалярного аргумента. Это имя может использоваться в *FunctionBody*. Оно привязано к определенному значению при вызове лямбда-функции.  |
| <ul><li>*ArgType* </li></ul>| Тип формального скалярного аргумента. | Сейчас в качестве типов аргументов лямбда-функций поддерживаются только следующие типы: `bool`, `string`, `long`, `datetime`, `timespan`, `real` и `dynamic` (и псевдонимы для этих типов).

> [!NOTE]
>Табличное выражение, используемое в вызове лямбда-функции, должно включать (среди прочего) все атрибуты с соответствующими типами.
>
>`(*)` можно использовать в качестве табличного выражения. 
>
> В вызове лямбда-функции можно использовать любое табличное выражение, и ни один из его столбцов не может быть получен в лямбда-выражении. 
>
> Все табличные аргументы должны располагаться перед скалярными аргументами.

## <a name="multiple-and-nested-let-statements"></a>Множественные и вложенные операторы let

Вы можете использовать несколько операторов let, разделив их точкой с запятой (`;`), как показано в примере ниже.

> [!NOTE]
> Последний оператор должен быть допустимым выражением запроса. 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

Кроме того, разрешены вложенные операторы let, которые можно использовать в лямбда-выражении.
Операторы let и их аргументы доступны в текущей и внутренней областях тела функции.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>Примеры

### <a name="use-let-function-to-define-constants"></a>Использование функции let для определения констант

В следующем примере имя `x` привязывается к скалярному литералу `1`, а затем используется в операторе табличного выражения.

```kusto
let x = 1;
range y from x to x step x
```

Этот пример эквивалентен предыдущему, только имя оператора let указывается с помощью понятия `['name']`.

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>Использование let для скалярных значений

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>Использование оператора let с аргументами для скалярного вычисления

В этом примере используется оператор let с аргументами для скалярного вычисления. Запрос определяет функцию `MultiplyByN` для умножения двух чисел.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let MultiplyByN = (val:long, n:long) { val * n };
range x from 1 to 5 step 1 
| extend result = MultiplyByN(x, 5)
```

|x|набор по|
|---|---|
|1|5|
|2|10|
|3|15|
|4|20|
|5|25|

В следующем примере из входных данных удаляются начальные и конечные символы `1`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let TrimOnes = (s:string) { trim("1", s) };
range x from 10 to 15 step 1 
| extend result = TrimOnes(tostring(x))
```

|x|набор по|
|---|---|
|10|0|
|11||
|12|2|
|13|3|
|14|4|
|15|5|


### <a name="use-multiple-let-statements"></a>Использование множественных операторов let

В этом примере определяются два оператора let, при этом один оператор (`foo2`) использует другой (`foo1`).

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>Использование ключевого слова `view` в операторе let

В этом примере показано, как использовать оператор let с ключевым словом `view`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Range10 = view () { range MyColumn from 1 to 10 step 1 };
let Range20 = view () { range MyColumn from 1 to 20 step 1 };
search MyColumn == 5
```

|$table|MyColumn|
|---|---|
|Range10|5|
|Range20|5|


### <a name="use-materialize-function"></a>Использование функции materialize

Функция [`materialize`](materializefunction.md) позволяет кэшировать результаты вложенных запросов во время выполнения запроса. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let totalPagesPerDay = PageViews
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
let materializedScope = PageViews
| summarize by Page, Day = startofday(Timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project Page, Day1 = Day
| join kind = inner
(
    cachedResult
    | project Page, Day2 = Day
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|День 1|День 2|Процент|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
