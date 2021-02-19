---
title: Оператор make-series в Azure Data Explorer
description: В этой статье описывается оператор make-series в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.localizationpriority: high
ms.openlocfilehash: 29fdacc9483c21c4a8a148d2134f4082d439bb89
ms.sourcegitcommit: ee49cd8186d4aecd5de1ed6d24db6c7b7a079ac4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549009"
---
# <a name="make-series-operator"></a>Оператор make-series

Узнайте, как создать серию указанных статистических значений по заданной оси.

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>Синтаксис

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* [`from` *start*] [`to` *end*] `step` *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>Аргументы

* *Column* — необязательное имя итогового столбца. По умолчанию это имя, получаемое из выражения.
* *DefaultValue* — значение по умолчанию, которое будет использоваться вместо отсутствующих значений. Если строка с определенными значениями *AxisColumn* и *GroupExpression* отсутствует, в результатах соответствующий элемент массива будет иметь значение *DefaultValue*. Если аргумент *DefaultValue* опущен, предполагается значение 0. 
* *Агрегирование.* Вызов [статистической функции](make-seriesoperator.md#list-of-aggregation-functions), например `count()` или `avg()`, с именами столбцов в качестве аргументов. См. [список статистических функций](make-seriesoperator.md#list-of-aggregation-functions). С оператором `make-series` можно использовать только статистические функции, возвращающие числовые результаты.
* *AxisColumn* — столбец, в котором упорядочивается ряд. Он может рассматриваться как временная шкала, но помимо `datetime` принимаются все числовые типы.
* *start* (необязательно) — значение нижней границы *AxisColumn* для каждого создаваемого ряда. *start*, *end* и *step* используются для создания массива значений *AxisColumn* в заданном диапазоне и с использованием указанного значения *step*. Все значения *Aggregation* упорядочиваются относительно этого массива. Этот массив *AxisColumn* также является последним выходным столбцом в выходных данных, имеющим такое же имя, что и *AxisColumn*. Если значение *start* не указано, началом будет первый сегмент (step), включающий данные в каждом ряду.
* *end* (необязательно) — значение верхней границы (без включения) *AxisColumn*. Последний индекс временного ряда меньше этого значения (и будет иметь значение *start* плюс целое число, кратное *step*, но меньше, чем *end*). Если значение *end* не указано, концом будет верхняя граница последнего сегмента (step), включающего данные по каждому ряду.
* *step* — разница между двумя последовательными элементами массива *AxisColumn* (то есть размер сегмента).
* *GroupExpression* — выражение для столбцов, предоставляющее набор уникальных значений. Обычно это имя столбца, который уже содержит ограниченный набор значений. 
* *MakeSeriesParameters* — ноль или более (разделенных пробелами) параметров в виде *имя* `=` *значение*, управляющих поведением. Поддерживаются следующие параметры: 
  
  |Имя           |Значения                                        |Описание                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |Выдает результат по умолчанию, если для оператора make-series отсутствуют входные данные.|                                

## <a name="returns"></a>Возвращаемое значение

Входные строки объединяются в группы с одинаковыми значениями выражений `by` и выражения `bin_at(`*AxisColumn*`, `*step*`, `*start*`)`. Затем указанные агрегатные функции выполняют вычисления и создают строку для каждой группы. Результат содержит столбцы `by`, столбец *AxisColumn* и хотя бы один столбец для каждого вычисленного статистического выражения. (Агрегирование, включающее несколько столбцов или нечисловые результаты, не поддерживается.)

Этот промежуточный результат содержит столько же строк, сколько имеется отдельных сочетаний `by` и значений `bin_at(`*AxisColumn*`, `*step*`, `*start*`)`.

Наконец, строки из промежуточного результата, собранные в группы с одинаковыми значениями выражений `by`, и все агрегированные значения упорядочиваются в массивы (значения типа `dynamic`). Для каждого агрегирования существует один столбец, содержащий массив с тем же именем. Последний столбец в выходных данных функции range со всеми значениями *AxisColumn*. Его значение повторяется для всех строк. 

Из-за заполнения отсутствующих сегментов значением по умолчанию результирующая сводная таблица имеет то же количество сегментов (то есть агрегированных значений) для всех рядов.  

**Примечание**

Для статистических выражений и выражений группирования допускаются произвольные выражения, но эффективнее использовать простые имена столбцов.

**Альтернативный синтаксис**

*T* `| make-series` [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* `in` `range(`*start*`,` *stop*`,` *step*`)` [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

Созданный ряд с альтернативным синтаксисом отличается от основного синтаксиса в двух аспектах:
* Значение *stop* является инклюзивным.
* Группирование оси индекса создается с помощью bin(), а не bin_at(). Это означает, что *start* может не включаться в созданный ряд.

Рекомендуется использовать основной синтаксис make-series, а не альтернативный.

**Распространение и смешение**

`make-series` поддерживает [указания shufflekey](shufflequery.md) для `summarize` с использованием синтаксиса hint.shufflekey.

## <a name="list-of-aggregation-functions"></a>Список статистических функций

|Функция|Описание|
|--------|-----------|
|[any()](any-aggfunction.md)|Возвращает случайное непустое значение в группе.|
|[avg()](avg-aggfunction.md)|Возвращает среднее значение в группе.|
|[avgif()](avgif-aggfunction.md)|Возвращает среднее значение с предикатом в группе.|
|[count()](count-aggfunction.md)|Возвращает число для групп.|
|[countif()](countif-aggfunction.md)|Возвращает число с предикатом для групп.|
|[dcount()](dcount-aggfunction.md)|Возвращает приблизительное число уникальных элементов в группе.|
|[dcountif()](dcountif-aggfunction.md)|Возвращает приблизительное число уникальных элементов с предикатом в группе.|
|[max()](max-aggfunction.md)|Возвращает максимальное значение в группе.|
|[maxif()](maxif-aggfunction.md)|Возвращает максимальное значение с предикатом в группе.|
|[min()](min-aggfunction.md)|Возвращает минимальное значение в группе.|
|[minif()](minif-aggfunction.md)|Возвращает минимальное значение с предикатом в группе.|
|[percentile()](percentiles-aggfunction.md)|Возвращает значение процентиля в группе.|
|[stdev()](stdev-aggfunction.md)|Возвращает стандартное отклонение в группе.|
|[sum()](sum-aggfunction.md)|Возвращает сумму элементов в группе|
|[sumif()](sumif-aggfunction.md)|Возвращает сумму элементов с предикатом в группе.|
|[variance()](variance-aggfunction.md)|Возвращает вариантность в группе.|

## <a name="list-of-series-analysis-functions"></a>Список функций анализа рядов

|Функция|Описание|
|--------|-----------|
|[series_fir()](series-firfunction.md)|Применяет фильтр с [конечной импульсной характеристикой](https://en.wikipedia.org/wiki/Finite_impulse_response).|
|[series_iir()](series-iirfunction.md)|Применяет фильтр с [бесконечной импульсной характеристикой](https://en.wikipedia.org/wiki/Infinite_impulse_response).|
|[series_fit_line()](series-fit-linefunction.md)|Находит прямую строку, которая является наилучшим приближением входных данных.|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Находит строку, которая является наилучшим приближением входных данных, возвращая динамический объект.|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Находит две строки, которые являются наилучшим приближением входных данных.|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Находит две строки, которые являются наилучшим приближением входных данных, возвращая динамический объект.|
|[series_outliers()](series-outliersfunction.md)|Оценивает точки аномалий в ряду.|
|[series_periods_detect()](series-periods-detectfunction.md)|Находит наиболее значимые периоды во временных рядах.|
|[series_periods_validate()](series-periods-validatefunction.md)|Проверяет, содержит ли временной ряд периодические шаблоны заданной длины.|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Возвращает несколько столбцов с общей статистикой (min/max/variance/stdev/average).|
|[series_stats()](series-statsfunction.md)|Создает динамическое значение с общей статистикой (min/max/variance/stdev/average).|

Полный список функций для анализа рядов см. в следующем документе: [Функции обработки рядов](scalarfunctions.md#series-processing-functions)

## <a name="list-of-series-interpolation-functions"></a>Список функций интерполяции ряда

|Функция|Описание|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|Выполняет интерполяцию с обратным заполнением отсутствующих значений в ряду.|
|[series_fill_const()](series-fill-constfunction.md)|Заменяет отсутствующие значения в ряду указанным значением константы.|
|[series_fill_forward()](series-fill-forwardfunction.md)|Выполняет интерполяцию с прямым заполнением отсутствующих значений в ряду.|
|[series_fill_linear()](series-fill-linearfunction.md)|Выполняет линейную интерполяцию отсутствующих значений в ряду.|

* Примечание. По умолчанию функции интерполяции воспринимают `null` как отсутствующее значение. Поэтому укажите `default=`*double*(`null`) в `make-series`, если планируется использовать функции интерполяции для ряда. 

## <a name="example"></a>Пример
  
 Таблица, в которой отображаются массивы чисел и средние цены для каждого фрукта у каждого поставщика, упорядоченные по метке времени с заданным диапазоном. В выходных данных есть строка для каждого отдельного сочетания фрукта и поставщика. В выходных столбцах указаны фрукт, поставщик и следующие массивы: количество, среднее значение и полная временная шкала (с 01.01.2016 по 10.01.2016). Все массивы сортируются по соответствующей метке времени, а все пробелы заполняются значениями по умолчанию (0 в этом примере). Все остальные входные столбцы игнорируются.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Три таблицы. В первой приведены необработанные данные, во второй — только различные сочетания &quot;поставщик — фрукт — дата&quot;, а в третьей — результаты make-series.":::  

<!-- csl: https://help.kusto.windows.net:443/Samples --> 
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| make-series avg(metric) on timestamp from stime to etime step interval 
```
  
|avg_metric|TIMESTAMP|
|---|---|
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00:00.0000000Z", "2017-01-03T00:00:00.0000000Z", "2017-01-04T00:00:00.0000000Z", "2017-01-05T00:00:00.0000000Z", "2017-01-06T00:00:00.0000000Z", "2017-01-07T00:00:00.0000000Z", "2017-01-08T00:00:00.0000000Z", "2017-01-09T00:00:00.0000000Z" ]|  


Если входные данные для `make-series` пусты, по умолчанию `make-series` также выводит пустой результат.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series avg(metric) default=1.0 on timestamp from stime to etime step interval 
| count 
```

|Счетчик|
|---|
|0|


Использование `kind=nonempty` в `make-series` приведет к непустому результату значений по умолчанию:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series kind=nonempty avg(metric) default=1.0 on timestamp from stime to etime step interval 
```

|avg_metric|TIMESTAMP|
|---|---|
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.0000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.0000000Z"<br>]|
