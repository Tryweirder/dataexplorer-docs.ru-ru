---
title: Разделирование и составление промежуточных результатов агрегирования - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается раздел и составление промежуточных результатов агрегирования в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c906e764330efedac051587201b28e5d0fc1ef07
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766135"
---
# <a name="partitioning-and-composing-intermediate-results-of-aggregations"></a>Раздел и составление промежуточных результатов агрегирования

Предположим, что вы хотите, чтобы вычислить количество отдельных пользователей за последние семь дней, каждый день. Один из способов сделать `summarize dcount(user)` это было бы запустить один раз в день с диапазоном фильтруется до последних семи дней. Это неэффективно, потому что каждый раз, когда вычисление выполнить есть шестидневное перекрытие с предыдущим расчетом. Другим вариантом является расчет некоторых агрегатов для каждого дня, а затем объединить эти агрегаты эффективным способом. Эта опция требует, чтобы вы "запомнить" последние шесть результатов, но это гораздо более эффективным.

Такие запросы на разделы были бы простыми для простых агрегатов, таких как количество () и сумма(). Возможно, однако, также выполнять их для более сложных агрегатов, таких как количество () и процентилей (). Эта тема объясняет, как Kusto поддерживает такие расчеты.

Следующие примеры показывают, как использовать hll/tdigest и показывает, что использование этих в некоторых сценариях может быть гораздо более performant, чем в других отношениях:

> [!NOTE]
> В некоторых случаях динамические `hll` объекты, генерируемые `tdigest` или агрегированными функциями, могут быть большими и превышать свойство MaxValueSize в политике кодирования по умолчанию. Если это так, объект будет попадает в нулевые.
Например, при сохранении `hll` вывода функции с уровнем точности 4, размер объекта hll превышает по умолчанию MaxValueSize, который составляет 1 МБ.

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

Затаив это в таблицу перед применением такого рода политики будет глотать нулю:

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

Чтобы избежать этого, используйте специальный `bigobject`тип политики кодирования, который переопределяет MaxValueSize до 2MB, как это:

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```



Так глотая значение сейчас к той же таблице выше:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```
глоток второго значения успешно : 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


**Пример**

Предположим, что есть таблица, PageViewsHllTDigest, которая имеет значения hll над Страницами, просматриваемыми в каждый час. Цель состоит в том, чтобы получить `12h`эти значения, но binned к , объединить значения hll `12h`с использованием hll_merge() агрегированной функции по метки времени binned к . Затем позвоните в функцию dcount_hll, чтобы получить окончательное значение количества:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Отметка времени|dcount_hll_merged_hll|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

Или даже для binned `1d`метки времени для:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Отметка времени|dcount_hll_merged_hll|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

 То же самое можно сделать по значениям tdigest, которые представляют BytesDelivered в каждый час:

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Отметка времени|percentile_tdigest_merged_tdigests|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
**Пример**

Ограничения Kusto достигаются с наборами данных, которые являются слишком большими, где необходимо запускать периодические запросы по набору данных, но запускать регулярные запросы для расчета [`percentile()`](percentiles-aggfunction.md) или [`dcount()`](dcount-aggfunction.md) над этими большими наборами данных.

::: zone pivot="azuredataexplorer"

Чтобы решить эту проблему, недавно добавленные данные могут быть добавлены [`hll()`](hll-aggfunction.md) в таблицу temp [`tdigest()`](tdigest-aggfunction.md) как значения hll или [`set/append`](../management/data-ingestion/index.md) [`update policy`](../management/updatepolicy.md)tdigest, используя, когда требуемая операция является обливанием или , В этом случае промежуточные результаты dcount или tdigest сохраняются в другой набор данных, который должен быть меньше, чем целевой большой.

::: zone-end

::: zone pivot="azuremonitor"

Для решения этой проблемы недавно добавленные данные могут быть добавлены [`hll()`](hll-aggfunction.md) в таблицу временных топов, поскольку значения hll или tdigest используются при подсчете требуемой операции, в этом случае промежуточные результаты dcount сохраняются в другой набор данных, который должен быть меньше целевого большого.

::: zone-end

Когда вам нужно получить окончательные результаты этих значений, запросы могут использовать [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md)слияния hll/tdigest: , [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) Затем, после получения объединенных значений, могут быть вызваны на эти объединенные значения, чтобы получить конечный результат dcount или процентилов.

Предполагая, что есть таблица, PageViews, в которую ежедневно попадает данные, каждый день, на который вы хотите вычислить четкое количество страниц, просматриваемых на минуит позже, чем дата и дата (2016-05-01 18:00:00000000).

Выполнить следующий запрос:

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|Отметка времени|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00:00.0000000|83634|20056275|
|2016-05-02 00:00:00.0000000|82770|64135183|
|2016-05-03 00:00:00.0000000|72920|13685621|

Этот запрос будет агрегировать все значения каждый раз при запуске этого запроса (например, если вы хотите запустить его много раз в день).

Если вы сохраните значения hll и tdigest (которые являются промежуточными результатами учета и процентиля) в таблицу temp, PageViewsHllTDigest, используя политику обновления или команды набора/приложения, вы можете только объединить значения, а затем использовать dcount_hll/percentile_tdigest, используя следующий запрос:

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Отметка времени|percentile_tdigest_merge_tdigests_tdigestBytesDel|dcount_hll_hll_merge_hllPage|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

Это должно быть более перформативным, и запрос проходит по меньшей таблице (в данном примере первый запрос выполняется более чем на 215 миллионов фунтов стерлингов, в то время как второй выполняет только 32 записи).

**Пример**

Запрос удержания.
Предполагая, что у вас есть таблица, которая обобщает, когда каждая страница Википедии была просмотрена (размер выборки составляет 10M), и вы хотите найти для каждой даты1 date2 процент страниц, рассмотренных как в date1, так и в date2 по отношению к страницам, просмотренным на date1 (дата1 < date2).
  
Тривиальный способ использует присоединиться и обобщить операторов:

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

 
Приведенный выше запрос занял 18 секунд.

При использовании [`hll()`](hll-aggfunction.md)функций, [`hll_merge()`](hll-merge-aggfunction.md) и [`dcount_hll()`](dcount-hllfunction.md), эквивалентный запрос закончится после 1,3 секунды и показывает, что hll функции ускоряет запрос выше в 14 раз:

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

|день1|день2|Процент|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> Результаты запросов не являются 100% точными из-за ошибки функций hll. (см. [`dcount()`](dcount-aggfunction.md) дополнительную информацию об ошибках).
