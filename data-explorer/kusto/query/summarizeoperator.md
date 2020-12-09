---
title: Оператор summarize в Azure Data Explorer | Документация Майкрософт
description: В этой статье описывается оператор summarize в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513205"
---
# <a name="summarize-operator"></a>Оператор summarize

Создает таблицу, которая объединяет содержимое входной таблицы.

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

Возвращает таблицу с указанием количества транзакций продаж и общей суммы в расчете на один фрукт и продажу в месяц.
Выходные столбцы указывают на количество транзакций, доход от транзакции, тип фрукта, а также дату и время начала месяца регистрации транзакции.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

В таблице отображено количество элементов с ценами в каждом интервале [0,10.0], [10.0,20.0] и т. д. Пример включает один столбец для подсчета и один столбец для диапазон цен. Все остальные входные столбцы игнорируются.

## <a name="syntax"></a>Синтаксис

*T* `| summarize` [[*Column* `=`] *Aggregation* [`,` ...]] [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>Аргументы

* *Column* — необязательное имя итогового столбца. По умолчанию это имя, получаемое из выражения.
* *Агрегирование.* Вызов [статистической функции](summarizeoperator.md#list-of-aggregation-functions), например `count()` или `avg()`, с именами столбцов в качестве аргументов. См. [список статистических функций](summarizeoperator.md#list-of-aggregation-functions).
* *GroupExpression.* Скалярное выражение, которое может ссылаться на входные данные.
  Выходные данные будут содержать такое количество записей, которое будет соответствовать количеству несовпадающих значений во всех выражениях групп.

> [!NOTE]
> Если таблица входных данных пуста, выходные данные зависят от того, используется ли *GroupExpression*.
>
> * Если свойство *GroupExpression* не предоставлено, вывод будет представлен одной (пустой) строкой.
> * Если же свойство *GroupExpression* предоставлено, вывод не будет иметь строк.

## <a name="returns"></a>Возвращаемое значение

Входные строки объединяются в группы с одинаковыми значениями выражений `by` . Затем указанные агрегатные функции выполняют вычисления и создают строку для каждой группы. Результат содержит столбцы `by` и хотя бы один столбец для каждого вычисленного статистического выражения. (Некоторые агрегатные функции возвращают несколько столбцов).

Результат содержит столько строк, сколько существует несовпадающих комбинаций значений `by` (которые могут быть равны нулю). Если ключи группы не указаны, в результате будет содержаться одна запись.

Чтобы суммировать диапазоны числовых значений, можно ограничить диапазоны дискретными значениями с помощью `bin()`.

> [!NOTE]
> * Для агрегатных выражений и выражений группирования допускаются произвольные выражения, но эффективнее использовать простые имена столбцов или функцию `bin()` для числовых столбцов.
> * Автоматические почасовые объединения для столбцов "Дата и время" больше не поддерживаются. Вместо этого используйте явное группирование. Например, `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Список статистических функций

|Функция|Описание|
|--------|-----------|
|[any()](any-aggfunction.md)|Возвращает случайное непустое значение в группе.|
|[anyif()](anyif-aggfunction.md)|Возвращает случайное непустое значение в группе (с предикатом)|
|[arg_max()](arg-max-aggfunction.md)|Возвращает одно или несколько выражений, если аргумент достигает максимума|
|[arg_min()](arg-min-aggfunction.md)|Возвращает одно или несколько выражений, если аргумент достигает минимума|
|[avg()](avg-aggfunction.md)|Возвращает среднее значение в группе.|
|[avgif()](avgif-aggfunction.md)|Возвращает среднее значение в группе (с предикатом)|
|[binary_all_and](binary-all-and-aggfunction.md)|Возвращает агрегированное значение с помощью логического `AND` группы|
|[binary_all_or](binary-all-or-aggfunction.md)|Возвращает агрегированное значение с помощью логического `OR` группы|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Возвращает агрегированное значение с помощью логического `XOR` группы|
|[buildschema()](buildschema-aggfunction.md)|Возвращает минимальную схему, которая допускает все значения входных данных `dynamic`|
|[count()](count-aggfunction.md)|Возвращает число для групп.|
|[countif()](countif-aggfunction.md)|Возвращает число с предикатом для групп.|
|[dcount()](dcount-aggfunction.md)|Возвращает приблизительное число различных элементов в группе|
|[dcountif()](dcountif-aggfunction.md)|Возвращает приблизительное число различных элементов в группе (с предикатом)|
|[make_bag()](make-bag-aggfunction.md)|Возвращает контейнер свойств динамических значений в группе|
|[make_bag_if()](make-bag-if-aggfunction.md)|Возвращает контейнер свойств динамических значений в группе (с предикатом)|
|[make_list()](makelist-aggfunction.md)|Возвращает список всех значений в группе|
|[make_list_if()](makelistif-aggfunction.md)|Возвращает список всех значений в группе (с предикатом)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Возвращает список всех значений в группе, включая значения NULL|
|[make_set()](makeset-aggfunction.md)|Возвращает набор несовпадающих значений в группе|
|[make_set_if()](makesetif-aggfunction.md)|Возвращает набор несовпадающих значений в группе (с предикатом)|
|[max()](max-aggfunction.md)|Возвращает максимальное значение в группе.|
|[maxif()](maxif-aggfunction.md)|Возвращает максимальное значение в группе (с предикатом)|
|[min()](min-aggfunction.md)|Возвращает минимальное значение в группе.|
|[minif()](minif-aggfunction.md)|Возвращает минимальное значение в группе (с предикатом)|
|[percentiles()](percentiles-aggfunction.md)|Возвращает приблизительное значение процентиля группы|
|[percentiles_array()](percentiles-aggfunction.md)|Возвращает приблизительные значения процентилей группы|
|[percentilesw()](percentiles-aggfunction.md)|Возвращает приблизительное взвешенное значение процентиля группы|
|[percentilesw_array()](percentiles-aggfunction.md)|Возвращает приблизительные взвешенные значения процентилей группы|
|[stdev()](stdev-aggfunction.md)|Возвращает стандартное отклонение в группе|
|[stdevif()](stdevif-aggfunction.md)|Возвращает стандартное отклонение в группе (с предикатом)|
|[sum()](sum-aggfunction.md)|Возвращает сумму элементов в группе|
|[sumif()](sumif-aggfunction.md)|Возвращает сумму элементов в группе (с предикатом)|
|[variance()](variance-aggfunction.md)|Возвращает вариантность в группе|
|[varianceif()](varianceif-aggfunction.md)|Возвращает вариантность в группе (с предикатом)|

## <a name="aggregates-default-values"></a>Значения по умолчанию для агрегатов

В следующей таблице перечислены значения по умолчанию для агрегатов:

Оператор       |Значение по умолчанию                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    пустой динамический массив              ([])          
 Все остальные          |   null                           

 Если использовать эти агрегаты для сущностей, которые содержат значения NULL, такие значения будут игнорироваться и не будут использоваться в вычислении (см. примеры ниже).

## <a name="examples"></a>Примеры

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="Суммировать цены по фруктам и поставщикам":::

## <a name="example-unique-combination"></a>Пример Уникальное сочетание

Определите уникальные сочетания `ActivityType` и `CompletionStatus` в таблице. Здесь нет статистических функций, только ключи group-by. В выходных данных будут показаны только столбцы для этих результатов:

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>Пример Минимальная и максимальная метки времени

Находит минимальную и максимальную метки времени для всех записей в таблице "Действия". Предложение group by отсутствует, поэтому в выходных данных всего одна строка:

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>Пример Количество несовпадающих

Создайте строку для каждого континента, отображающую количество городов, в которых выполняются действия. Поскольку для свойства "continent" существует несколько значений, в предложении "by" не требуется функция группирования:

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>Пример Гистограмма

В следующем примере вычисляется гистограмма для каждого типа действия. Поскольку `Duration` имеет много значений, используйте `bin`, чтобы сгруппировать его значения в 10-минутные интервалы:

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

**Пример значений по умолчанию для агрегатов**

Если во входных данных оператора `summarize` содержится хотя бы один пустой ключ group-by, результат не будет содержать выходных данных.

Если же входные данные оператора `summarize` не содержат пустой ключ group-by, результатом будут значения по умолчанию для агрегатов, используемых в операторе `summarize`:

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

Агрегатная функция avg суммирует все значения, отличные от NULL, и подсчитывает только те, которые использовались в вычислении (не принимает во внимание значения NULL).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

При обычном расчете значения NULL будут учитываться: 

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
|[5.0]|[5.0]|
