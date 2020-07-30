---
title: Оператор суммирования в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор суммирования в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: a200d0619b25fe7410a82a941a3b1bf6e35d60ac
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342620"
---
# <a name="summarize-operator"></a>Оператор summarize

Создает таблицу, которая объединяет содержимое входной таблицы.

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

Таблица, показывающая число и среднюю цену каждого из фруктов каждого из поставщиков. В выходных данных есть строка для каждого отдельного сочетания фруктов и поставщика. В выходных столбцах отображается количество, средняя цена, фруктов и поставщик. Все остальные входные столбцы игнорируются.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

В таблице отображено количество элементов с ценами в каждом интервале [0,10.0], [10.0,20.0] и т. д. Пример включает один столбец для подсчета и один столбец для диапазон цен. Все остальные входные столбцы игнорируются.

## <a name="syntax"></a>Синтаксис

*T* `| summarize` [[*столбец* `=` ] *агрегирование* [ `,` ...]] [ `by` [*столбец* `=` ] *GroupExpression* [ `,` ...]]

## <a name="arguments"></a>Аргументы

* *Column* — необязательное имя итогового столбца. По умолчанию это имя, получаемое из выражения.
* *Статистическая обработка:* Вызов [агрегатной функции](summarizeoperator.md#list-of-aggregation-functions) , такой как `count()` или `avg()` , с именами столбцов в качестве аргументов. См. [список статистических функций](summarizeoperator.md#list-of-aggregation-functions).
* *GroupExpression* — выражение для столбцов, предоставляющее набор уникальных значений. Обычно это имя столбца, который уже содержит ограниченный набор значений, либо функция `bin()` с числовым или временным столбцом в качестве аргумента. 

> [!NOTE]
> Если входная таблица пуста, выходные данные зависят от того, используется ли *GroupExpression* :
>
> * Если *GroupExpression* не указан, то выходные данные будут одной (пустой) строкой.
> * Если указан параметр *GroupExpression* , выходные данные не будут иметь строк.

## <a name="returns"></a>Результаты

Входные строки объединяются в группы с одинаковыми значениями выражений `by` . Затем указанные агрегатные функции выполняют вычисления и создают строку для каждой группы. Результат содержит столбцы `by` и хотя бы один столбец для каждого вычисленного статистического выражения. (Некоторые агрегатные функции возвращают несколько столбцов).

Результат содержит столько строк, сколько существует различных сочетаний `by` значений (которые могут быть равны нулю). Если ключи группы не указаны, результат будет иметь одну запись.

Для суммирования диапазонов числовых значений используйте, `bin()` чтобы сократить диапазоны до дискретных значений.

> [!NOTE]
> * Для агрегатных выражений и выражений группирования допускаются произвольные выражения, но эффективнее использовать простые имена столбцов или функцию `bin()` для числовых столбцов.
> * Автоматические почасовые ячейки для столбцов datetime больше не поддерживаются. Вместо этого используйте явный группирования. Например, `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Список статистических функций

|Компонент|Описание|
|--------|-----------|
|[Any ()](any-aggfunction.md)|Возвращает случайное непустое значение для группы|
|[anyif()](anyif-aggfunction.md)|Возвращает случайное непустое значение группы (с предикатом with)|
|[arg_max()](arg-max-aggfunction.md)|Возвращает одно или несколько выражений, если аргумент является развернутым|
|[arg_min()](arg-min-aggfunction.md)|Возвращает одно или несколько выражений, если аргумент является минимальным|
|[AVG ()](avg-aggfunction.md)|Возвращает среднее значение в группе|
|[avgif()](avgif-aggfunction.md)|Возвращает среднее значение в группе (с предикатом)|
|[binary_all_and](binary-all-and-aggfunction.md)|Возвращает агрегированное значение с помощью двоичного `AND` объекта группы|
|[binary_all_or](binary-all-or-aggfunction.md)|Возвращает агрегированное значение с помощью двоичного `OR` объекта группы|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Возвращает агрегированное значение с помощью двоичного `XOR` объекта группы|
|[buildschema()](buildschema-aggfunction.md)|Возвращает минимальную схему, которая отменяет все значения `dynamic` входных данных|
|[Count ()](count-aggfunction.md)|Возвращает количество групп|
|[countif()](countif-aggfunction.md)|Возвращает число с предикатом группы|
|[dcount()](dcount-aggfunction.md)|Возвращает приблизительное число различных элементов группы.|
|[dcountif()](dcountif-aggfunction.md)|Возвращает приблизительное число различных элементов группы (с предикатом with).|
|[make_bag()](make-bag-aggfunction.md)|Возвращает контейнер свойств динамических значений в группе|
|[make_bag_if()](make-bag-if-aggfunction.md)|Возвращает контейнер свойств динамических значений в группе (с предикатом)|
|[make_list()](makelist-aggfunction.md)|Возвращает список всех значений в группе|
|[make_list_if()](makelistif-aggfunction.md)|Возвращает список всех значений в группе (с предикатом with).|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Возвращает список всех значений в группе, включая значения NULL.|
|[make_set()](makeset-aggfunction.md)|Возвращает набор различных значений в группе|
|[make_set_if()](makesetif-aggfunction.md)|Возвращает набор различных значений в группе (с предикатом with).|
|[max()](max-aggfunction.md)|Возвращает максимальное значение в группе.|
|[maxif()](maxif-aggfunction.md)|Возвращает максимальное значение в группе (с предикатом)|
|[min()](min-aggfunction.md)|Возвращает минимальное значение в группе.|
|[minif()](minif-aggfunction.md)|Возвращает минимальное значение в группе (с предикатом)|
|[процентили ()](percentiles-aggfunction.md)|Возвращает приблизительное значение процентиля группы|
|[percentiles_array ()](percentiles-aggfunction.md)|Возвращает процентили приблизительную часть группы|
|[перцентилесв ()](percentiles-aggfunction.md)|Возвращает приближенное взвешенное значение процентиля группы|
|[percentilesw_array ()](percentiles-aggfunction.md)|Возвращает взвешенное процентилиное приближение группы|
|[STDEV ()](stdev-aggfunction.md)|Возвращает стандартное отклонение по группе|
|[stdevif()](stdevif-aggfunction.md)|Возвращает стандартное отклонение по группе (с предикатом)|
|[Sum ()](sum-aggfunction.md)|Возвращает сумму элементов с группой|
|[sumif()](sumif-aggfunction.md)|Возвращает сумму элементов с помощью группы (предикат WITH)|
|[variance()](variance-aggfunction.md)|Возвращает дисперсию по группе|
|[varianceif()](varianceif-aggfunction.md)|Возвращает дисперсию по группе (с предикатом)|

## <a name="aggregates-default-values"></a>Выполняет статистическое вычисление значений по умолчанию

В следующей таблице перечислены значения по умолчанию для агрегатов.

Оператор       |Значение по умолчанию                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    пустой динамический массив ([])          
 Все остальные          |   null                           

 При использовании этих статистических функций для сущностей, которые содержат значения NULL, значения null будут игнорироваться и не будет участвовать в вычислении (см. примеры ниже).

## <a name="examples"></a>Примеры

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="Суммировать цены по фруктовам и поставщикам":::

## <a name="example"></a>Пример

Определите уникальные сочетания `ActivityType` и `CompletionStatus` в таблице. Нет статистических функций, просто сгруппированных по ключам. В выходных данных будут показаны только столбцы для этих результатов:

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example"></a>Пример

Находит минимальную и максимальную отметку времени всех записей в таблице действий. Предложение group by отсутствует, поэтому в выходных данных всего одна строка:

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example"></a>Пример

Создайте строку для каждого континента, показывающую количество городов, в которых выполняются действия. Так как существует несколько значений для "континент", в предложении "by" не требуется никакой функции группирования:

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example"></a>Пример

В следующем примере вычисляется гистограмма для каждого типа действия. Поскольку `Duration` имеет много значений, используйте `bin` для группирования его значений в 10-минутные интервалы:

```kusto
Activities | summarize count() by ActivityType, length=bin(Duration, 10m)
```

|`count_`|`ActivityType`|`length`
|---:|---|---
|`354`| `dancing` | `0:00:00.000`
|`23`|`singing` | `0:00:00.000`
|`2717`|`dancing`|`0:10:00.000`
|`341`|`singing`|`0:10:00.000`
|`725`|`dancing`|`0:20:00.000`
|`2876`|`singing`|`0:20:00.000`
|...

**Пример статистических значений по умолчанию**

Если входные данные `summarize` оператора имеют по крайней мере один пустой ключ Group-By, результат также будет пустым.

Если входные данные `summarize` оператора не имеют пустого ключа Group-By, результатом являются значения по умолчанию для статистических выражений, используемых в `summarize` :

```kusto
datatable(x:long)[]
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
datatable(x:long)[]
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
datatable(x:long)[]
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

Функция AVG со средним значением суммирует все значения, отличные от NULL, и подсчитывает только те, которые участвовали в вычислении (не принимает значения NULL в учетной записи).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

Регулярное число будет считать значения NULL: 

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize count(y)
```

|count_y|
|---|
|2|

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize make_set(y), make_set(y)
```

|set_y|set_y1|
|---|---|
|[5,0]|[5,0]|
