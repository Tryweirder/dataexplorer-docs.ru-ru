---
title: '& секций Kusto создание промежуточных результатов агрегирования — обозреватель данных Azure'
description: В этой статье описывается секционирование и составление промежуточных результатов агрегатов в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: bd3e7a77a4de46b6dcebb2f58c98009a9edddb43
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338615"
---
# <a name="using-hll-and-tdigest"></a>Использование hll() и tdigest()

Предположим, что необходимо вычислить количество уникальных пользователей каждый день за последние семь дней. Вы можете запустить `summarize dcount(user)` один раз в день с диапазоном, отфильтрованным за последние семь дней. Этот метод неэффективен, так как при каждом запуске вычисления существует шесть дней с предыдущим вычислением. Можно также вычислить статистическое выражение для каждого дня, а затем объединить эти статистические выражения. Этот метод требует «запомнить» последние шесть результатов, но гораздо эффективнее.

Секционированные запросы, как описано, просты для простых статистических выражений, таких как `count()` и `sum()` . Он также может быть полезен для сложных агрегатов, таких как `dcount()` и `percentiles()` . В этом разделе объясняется, как Kusto поддерживает такие вычисления.

В следующих примерах показано, как использовать `hll` / `tdigest` и продемонстрировать, что использование этих команд очень производительно в некоторых сценариях:

> [!NOTE]
> В некоторых случаях динамические объекты, созданные `hll` или `tdigest` агрегатными функциями, могут быть большими и превышать свойство максвалуесизе по умолчанию в политике кодирования. Если да, то объект будет принимать значение null.
Например, при сохранении выходных данных `hll` функции с уровнем точности 4 размер `hll` объекта превышает значение по умолчанию максвалуесизе, равное 1 МБ.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1,0000524520874|

При приеме этого объекта в таблицу перед применением этого типа политики будет принимать значение NULL:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| Column1 |
|---------|
| 1       |

Чтобы избежать приема значения NULL, используйте специальный тип политики кодирования `bigobject` , который переопределяет значение `MaxValueSize` на 2 МБ следующим образом:

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```

Принимаете значение сейчас в ту же таблицу, которая приведена выше:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

принимает второе значение успешно: 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


## <a name="example"></a>Пример

Существует таблица, `PageViewsHllTDigest` содержащая `hll` значения страниц, просматриваемых каждый час. Вы хотите, чтобы эти значения Binned `12h` . Объедините `hll` значения с помощью `hll_merge()` агрегатной функции с меткой времени Binned в `12h` . Используйте функцию, `dcount_hll` чтобы вернуть конечное `dcount` значение:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

Отметка времени ячейки для `1d` :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

Один и тот же запрос может быть выполнен по значениям `tdigest` , которые представляют `BytesDelivered` в каждый час:

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Timestamp|`percentile_tdigest_merged_tdigests`|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
## <a name="example"></a>Пример

Ограничения Kusto достигнуты с использованием слишком больших наборов данных, где необходимо выполнять периодические запросы к набору DataSet, но выполнять обычные запросы для вычисления [`percentile()`](percentiles-aggfunction.md) или [`dcount()`](dcount-aggfunction.md) поверх больших наборов данных.

::: zone pivot="azuredataexplorer"

Чтобы решить эту проблему, новые добавленные данные могут быть добавлены во временную таблицу как `hll` или `tdigest` значения с помощью [`hll()`](hll-aggfunction.md) , если требуемая операция — `dcount` или [`tdigest()`](tdigest-aggfunction.md) Если требуемая операция является процентилем с помощью [`set/append`](../../ingest-data-overview.md) или [`update policy`](../management/updatepolicy.md) . В этом случае промежуточные результаты `dcount` или `tdigest` сохраняются в другом наборе данных, который должен быть меньше целевого размера.

::: zone-end

::: zone pivot="azuremonitor"

Чтобы решить эту проблему, новые добавленные данные могут быть добавлены во временную таблицу как `hll` или `tdigest` значения с помощью [`hll()`](hll-aggfunction.md) , если требуется операция `dcount` . В этом случае промежуточные результаты `dcount` сохраняются в другом наборе данных, который должен быть меньше целевого размера.

::: zone-end

Если необходимо получить окончательные результаты этих значений, запросы могут использовать `hll` / `tdigest` слияния: [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md) . Затем, после получения Объединенных значений, [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) можно вызвать эти объединенные значения, чтобы получить окончательный результат `dcount` или процентили.

Предположим, что имеется таблица PageViews, в которую данные ежедневно принимаются каждый день, когда необходимо вычислить различные числа страниц, просматриваемых в минуту позднее даты = DateTime (2016-05-01 18:00:00.0000000).

Выполните следующий запрос:

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|Timestamp|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00:00.0000000|83634|20056275|
|2016-05-02 00:00:00.0000000|82770|64135183|
|2016-05-03 00:00:00.0000000|72920|13685621|

Этот запрос будет выполнять статистическую обработку всех значений при каждом выполнении этого запроса (например, если требуется запускать его несколько раз в день).

При сохранении `hll` `tdigest` значений и (которые являются промежуточными результатами `dcount` и процентиль) во временную таблицу, `PageViewsHllTDigest` , с помощью политики обновления или команд Set/Append, можно выполнить слияние только значений, а затем использовать `dcount_hll` / `percentile_tdigest` следующий запрос:

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Timestamp|`percentile_tdigest_merge_tdigests_tdigestBytesDel`|`dcount_hll_hll_merge_hllPage`|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

Этот запрос должен быть более производительным, так как он выполняется более чем с таблицей меньшего размера. В этом примере первый запрос выполняется более чем на каждые ~ 215M записей, а второй — только с 32 записями:

## <a name="example"></a>Пример

Запрос на сохранение.
Предположим, что у вас есть таблица, которая суммируется при просмотре каждой страницы Википедии (размер выборки — 10 миллионов), и вы хотите найти для каждой таблицы Date1 процент страниц, проверенных в Дата1 и Date2 относительно страниц, просматриваемых в Date1 (Дата1 < Date2).
  
Простой способ использует операторы Join и обобщить:

```kusto
// Get the total pages viewed each day
let totalPagesPerDay = PageViewsSample
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
// Join the table to itself to get a grid where 
// each row shows foreach page1, in which two dates
// it was viewed.
// Then count the pages between each two dates to
// get how many pages were viewed between date1 and date2.
PageViewsSample
| summarize by Page, Day1 = startofday(Timestamp)
| join kind = inner
(
    PageViewsSample
    | summarize by Page, Day2 = startofday(Timestamp)
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

 
Выполнение приведенного выше запроса заняло 18 секунд.

При использовании [`hll()`](hll-aggfunction.md) [`hll_merge()`](hll-merge-aggfunction.md) функций, и [`dcount_hll()`](dcount-hllfunction.md) эквивалентный запрос будет завершаться через ~ 1,3 секунд и показывать, что `hll` функции ускоряет выполнение запроса выше в ~ 14 раз:

```kusto
let Stats=PageViewsSample | summarize pagehll=hll(Page, 2) by day=startofday(Timestamp); // saving the hll values (intermediate results of the dcount values)
let day0=toscalar(Stats | summarize min(day)); // finding the min date over all dates.
let dayn=toscalar(Stats | summarize max(day)); // finding the max date over all dates.
let daycount=tolong((dayn-day0)/1d); // finding the range between max and min
Stats
| project idx=tolong((day-day0)/1d), day, pagehll
| mv-expand pidx=range(0, daycount) to typeof(long)
// Extend the column to get the dcount value from hll'ed values for each date (same as totalPagesPerDay from the above query)
| extend key1=iff(idx < pidx, idx, pidx), key2=iff(idx < pidx, pidx, idx), pages=dcount_hll(pagehll)
// For each two dates, merge the hll'ed values to get the total dcount over each two dates, 
// This helps to get the pages viewed in both date1 and date2 (see the description below about the intersection_size)
| summarize (day1, pages1)=arg_min(day, pages), (day2, pages2)=arg_max(day, pages), union_size=dcount_hll(hll_merge(pagehll)) by key1, key2
| where day2 > day1
// To get pages viewed in date1 and also date2, look at the merged dcount of date1 and date2, subtract it from pages of date1 + pages on date2.
| project pages1, day1,day2, intersection_size=(pages1 + pages2 - union_size)
| project day1, day2, Percentage = intersection_size*100.0 / pages1
```

|day1|day2|Процент|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> Результаты запросов не 100% в точности из-за ошибки `hll` функций. Дополнительные сведения об ошибках см. в разделе [`dcount()`](dcount-aggfunction.md) .
