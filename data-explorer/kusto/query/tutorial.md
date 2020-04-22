---
title: Учебник - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается учебник в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4720d44396fbb30350a4113fa798d7d179d7ae85
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765764"
---
# <a name="tutorial"></a>Учебник

::: zone pivot="azuredataexplorer"

Лучший способ узнать о языке запроса Kusto — посмотреть на некоторые простые запросы, чтобы получить «чувство» для языка с помощью [базы данных с некоторыми выборочными данными.](https://help.kusto.windows.net/Samples) Запросы, продемонстрированы в этой статье, должны работать в этой базе данных. Таблица `StormEvents` в этой выборочной базе данных содержит некоторую информацию о штормах, которые произошли в США.

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>Считать строки

Наша примерная база `StormEvents`данных имеет таблицу под названием .
Чтобы узнать, насколько он велик, мы свайку его содержимого в оператора, который просто считает строки:

* *Синтаксис:* Запрос — это источник данных (обычно название таблицы), за которым дополнительно следуют одна или несколько пар символа трубы и некоторых таблетных операторов.

```kusto
StormEvents | count
```

Ниже приведен результат:

|Count|
|-----|
|59066|
    
[счет оператора](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>проект: выберите подмножество столбцов

Используйте [проект,](./projectoperator.md) чтобы выбрать только столбцы, которые вы хотите. Смотрите пример ниже, который использует как [проект,](./projectoperator.md) так и [оператора.](./takeoperator.md)

## <a name="where-filtering-by-a-boolean-expression"></a>где: фильтрация по выражению булеана

Давайте посмотрим только `flood`s `California` в течение февраля-2007:

```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|Состояние|EventType|ЭпизодПовестнарный|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Калифорния|Наводнение|Фронтальная система, движущаяся через южную долину Сан-Хоакин, принесла кратковременные периоды проливных дождей в западный округ Керн ранним утром 19-го. Незначительное наводнение было сообщено через шоссе 166 штата вблизи Тафт.|

## <a name="take-show-me-n-rows"></a>взять: показать мне n строк

Давайте просмотрим некоторые данные — что содержится в выборке из 5 строк?

```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|Состояние|Описание события (EventNarrative)|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Сильный дождь|Флорида|Целых 9 дюймов дождя выпало в 24-часовой период в некоторых частях прибрежного округа Волусия.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Торнадо|Флорида|Торнадо обрушился в городе Евстифеев в северной части западного Кривого озера. Торнадо быстро усилился до силы EF1, когда он двигался на северо-запад через Евстифеев. Трасса была чуть менее двух миль в длину и была максимальная ширина 300 ярдов.  Торнадо разрушил 7 домов. Двадцать семь домов получили серьезные повреждения, а 81 дом сообщил о незначительных повреждениях. Серьезных травм не было, а материальный ущерб был установлен в размере 6,2 миллиона долларов.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Водяной сот|АТЛАНТИЧЕСКИЙ ЮГ|В Атлантическом океане к юго-востоку от Мельбурн-Бич образовалась водяная вода, которая ненадолго двинулась к берегу.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Шквалистый ветер|Миссисипи|Многочисленные большие деревья были снесены с некоторыми вниз на линиях электропередач. Ущерб произошел в восточной части округа Адамс.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Шквалистый ветер|Грузия|Уезд диспетчерской сообщили несколько деревьев были снесены вдоль Квинси Баттен Loop возле State Road 206. Была подсчитана стоимость удаления деревьев.|

Но [выводит](./takeoperator.md) строки из таблицы без определенного порядка, поэтому давайте отсортовать их.
* [предел](./takeoperator.md) является псевдонимом для [дубля](./takeoperator.md) и будет иметь тот же эффект.

## <a name="sort-and-top"></a>сортировка и верхняя

* *Синтаксис:* Некоторые операторы имеют параметры, введенные ключевыми словами, такими как `by`.
* `desc` — по убыванию, `asc` — по возрастанию.

Показать первые n строк, упорядоченных по одному из столбцов:

```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|Состояние|Описание события (EventNarrative)|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зимний шторм|Мичиган|Это сильное снегопадовое мероприятие продолжалось до раннего утра в День Нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зимний шторм|Мичиган|Это сильное снегопадовое мероприятие продолжалось до раннего утра в День Нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зимний шторм|Мичиган|Это сильное снегопадовое мероприятие продолжалось до раннего утра в День Нового года.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Высокий ветер|Калифорния|Северный северо-восточный ветер порывами до 58 миль / ч были зарегистрированы в горах округа Вентура.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Высокий ветер|Калифорния|Теплый Спрингс RAWS датчик сообщил северный ветер порывами до 58 миль / ч.|

То же самое может быть достигнуто с помощью [сортировки,](./sortoperator.md) а затем [взять](./takeoperator.md) оператора

```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndLat, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>расширить: вычислить производные столбцы

Создайте новый столбец, вычисляя значение в каждой строке:

```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|EventType|Состояние|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Сильный дождь|Флорида|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Торнадо|Флорида|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Водяной сот|АТЛАНТИЧЕСКИЙ ЮГ|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Шквалистый ветер|Миссисипи|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Шквалистый ветер|Грузия|

Можно повторно использовать имя столбца и присвоить результат расчета одной и той же колонке.
Пример:

```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[Scalar выражения](./scalar-data-types/index.md) могут включать в`+`себя `-` `*`все `/` `%`обычные операторы (, , , , , и есть целый ряд полезных функций.

## <a name="summarize-aggregate-groups-of-rows"></a>обобщенные: агрегированные группы строк

Подсчитайте, сколько событий приходит из каждой страны:

```kusto
StormEvents
| summarize event_count = count() by State
```

[суммировать](./summarizeoperator.md) группы вместе строки, которые `by` имеют те же значения в предложении, а затем использует функцию агрегации `count`(например), чтобы объединить каждую группу в одну строку. Так что в этом случае есть строка для каждого состояния и столбец для подсчета строк в этом состоянии.

Существует целый ряд [функций агрегирования,](./summarizeoperator.md#list-of-aggregation-functions)и вы можете использовать несколько из них в одном обобщенный оператор для производства нескольких вычисленных столбцов. Например, мы могли бы получить количество штормов в каждом штате, а также сумму уникального типа штормов в каждом штате,  
то мы могли бы использовать [сверху,](./topoperator.md) чтобы получить наиболее шторм пострадавших государств:

```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|Состояние|StormCount|TypeOfStorms|
|---|---|---|
|Техас|4701|27|
|Канзас|3166|21|
|Айова|2337|19|
|Иллинойс|2022|23|
|Миссури|2016|20|

Результат суммирования имеет:

* каждый столбец, указанный в `by`;
* столбец для каждого вычисленных выражений;
* строку для каждого сочетания значений `by` .

## <a name="summarize-by-scalar-values"></a>Суммирование по скалярным значениям

В `by` предложении можно использовать значения scaleric (число, время или интервал), но вы хотите поместить значения в ячейки.  
Функция [бен()](./binfunction.md) полезна для этого:

```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Это уменьшает все временные метки до интервалов 1 день:

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[Ячейка ()](./binfunction.md) такая же, как [пол ()](./floorfunction.md) функция на многих языках. Он просто уменьшает каждое значение до ближайшего кратного модуля, который вы поставляете, так что [обобщение](./summarizeoperator.md) может назначить строки группам.

## <a name="render-display-a-chart-or-table"></a>Рендеринг: отображение диаграммы или таблицы

Проект два столбца и использовать их в качестве x и y оси диаграммы:

```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tour/060.png" alt-text="060":::

Хотя мы `mid` удалили в работе проекта, мы все еще нуждаемся в ней, если мы хотим, чтобы диаграмма отображала страны в этом порядке.

Строго говоря, "рендер" является особенностью клиента, а не частью языка запроса. Тем не менее, он интегрирован в язык и очень полезен для представления результатов.


## <a name="timecharts"></a>Временные диаграммы

Возвращаясь к числовым ячейкам, давайте отобразим временные ряды:

```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tour/080.png" alt-text="080":::

## <a name="multiple-series"></a>Множественные серии

Используйте несколько значений в предложении `summarize by` , чтобы создать отдельную строку для каждого сочетания значений.

```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tour/090.png" alt-text="090":::

Просто добавьте термин рендера к вышеуказанному: `| render timechart`.

:::image type="content" source="images/tour/100.png" alt-text="100":::

Обратите `render timechart` внимание, что первый столбец используется в качестве оси x, а затем отображает другие столбцы в виде отдельных строк.

## <a name="daily-average-cycle"></a>Ежедневный средний цикл

Как меняется активность в течение обычного дня?

Считайте события к тому времени, modulo один день, binned в часы. Обратите внимание, `floor` что мы используем вместо бен:

```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tour/120.png" alt-text="120":::

В `render` настоящее время, не маркировать продолжительности должным образом, но мы могли бы использовать `| render columnchart` вместо:

:::image type="content" source="images/tour/110.png" alt-text="110":::

## <a name="compare-multiple-daily-series"></a>Сравнение серий для нескольких дней

Как меняется активность в течение времени суток в разных состояниях?

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tour/130.png" alt-text="130":::

Разделите, `1h` чтобы превратить ось X в часовое число вместо продолжительности:

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tour/140.png" alt-text="140":::

## <a name="join"></a>join

Как найти для двух данных EventTypes в каком состоянии оба они произошли?

Вы можете вытащить события шторма с первым EventType и с второго EventType, а затем присоединиться к двум наборам на государство.

```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tour/145.png" alt-text="145":::

## <a name="user-session-example-of-join"></a>Пример соединения сеанса пользователя

В этом разделе `StormEvents` не используется таблица.

Предположим, у вас есть данные, включающие события, обозначающие начало и конец каждого сеанса пользователя, с уникальным идентификатором для каждого сеанса. 

Как долго длится каждая пользовательская сессия?

С `extend` помощью псевдонима для двух меток времени можно вычислить продолжительность сеанса.

```kusto
Events
| where eventName == "session_started"
| project start_time = timestamp, stop_time, country, session_id
| join ( Events
    | where eventName == "session_ended"
    | project stop_time = timestamp, session_id
    ) on session_id
| extend duration = stop_time - start_time
| project start_time, stop_time, country, duration
| take 10
```

:::image type="content" source="images/tour/150.png" alt-text="150":::

Рекомендуется использовать `project` только для выбора необходимых столбцов перед выполнением соединения.
В тех же предложениях мы переименуем столбец timestamp.

## <a name="plot-a-distribution"></a>Построение графика распределения

Сколько бурь разной длины?

```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m)
| sort by duration asc
| render timechart
```

:::image type="content" source="images/tour/170.png" alt-text="170":::

Или `| render columnchart`используйте:

:::image type="content" source="images/tour/160.png" alt-text="160":::

## <a name="percentiles"></a>Процентили

Какие диапазоны продолжительности охватывают различные проценты штормов?

Используйте вышеуказанный запрос, но замените: `render`

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

В этом случае мы `by` не предоставили никаких условий, так что в результате получается одна строка:

:::image type="content" source="images/tour/180.png" alt-text="180":::

Из полученных результатов мы видим следующее:

* 5% штормов имеют продолжительность менее 5 м;
* 50% штормов длятся менее 1ч 25м;
* 5% штормов длятся не менее 2ч 50 м.

Чтобы получить отдельную разбивку для каждого состояния, мы просто должны привести столбец состояния отдельно через обоих обобщает операторов:

```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m), State
| sort by duration asc
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

:::image type="content" source="images/tour/190.png" alt-text="190":::

## <a name="let-assign-a-result-to-a-variable"></a>Оператор let: присвоение результата переменной

[Используйте, чтобы](./letstatement.md) отделить части выражения запроса в примере 'присоединиться' выше. Результаты не изменились:

```kusto
let LightningStorms = 
    StormEvents
    | where EventType == "Lightning";
let AvalancheStorms = 
    StormEvents
    | where EventType == "Avalanche";
LightningStorms 
| join (AvalancheStorms) on State
| distinct State
```

> Совет: В клиенте Kusto, не ставьте пустые линии между частями этого. Обязательно выполните все части.

## <a name="combining-data-from-several-databases-in-a-query"></a>Объединение данных из нескольких баз данных в запросе

Посмотреть [запросы кросс-базы данных](./cross-cluster-or-database-queries.md) для детального обсуждения

Когда вы пишете запрос стиля:

```kusto
Logs | where ...
```

Таблица с именем Журналы должна быть в вашей базе данных по умолчанию. Если вы хотите получить доступ к таблицам из другой базы данных, используйте следующий синтаксис:

```kusto
database("db").Table
```

Так что если у вас есть базы данных под названием *Диагностика* и *Телеметрия* и хотите соотнести некоторые из их данных, вы можете написать (при *условии, что диагностика* является вашей базой данных по умолчанию)

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

или если ваша база данных по умолчанию *является телеметрия*

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Все вышесказанное предполагало, что обе базы данных находятся в кластере, к которому вы в настоящее время подключены. Предположим, что база данных *Телеметрии* принадлежала другому кластеру под названием *TelemetryCluster.kusto.windows.net* затем для доступа к ней, вам понадобится

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> Примечание: при указании кластера база данных является обязательной

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
