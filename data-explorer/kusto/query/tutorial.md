---
title: Учебник. запросы Kusto в Azure обозреватель данных & Azure Monitor
description: В этом учебнике описывается, как использовать запросы на языке запросов Kusto для удовлетворения распространенных потребностей запросов в обозреватель данных и Azure Monitor Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8a47c51aa7924a28b27602056ea869bfd7a09936
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783813"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>Учебник. Использование запросов Kusto в Azure обозреватель данных и Azure Monitor

::: zone pivot="azuredataexplorer"

Лучший способ узнать о языке запросов Kusto — просмотреть некоторые базовые запросы, чтобы получить «впечатление» для языка. Рекомендуется использовать [базу данных с несколькими демонстрационными данными](https://help.kusto.windows.net/Samples). Запросы, показанные в этом руководстве, должны выполняться в этой базе данных. `StormEvents`Таблица в образце базы данных содержит некоторые сведения о выделах, произошедших в США.

## <a name="count-rows"></a>Считать строки

В нашем примере базы данных имеется таблица с именем `StormEvents` . Чтобы узнать, насколько велика таблица, мы будем передавать ее содержимое в оператор, который просто подсчитывает строки таблицы. 

*Примечание синтаксиса*. запрос — это источник данных (обычно имя таблицы), за которым может следовать одна или несколько пар символа вертикальной черты и некоторый табличный оператор.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

Выходные данные будут выглядеть следующим образом.

|Счетчик|
|-----|
|59066|
    
Дополнительные сведения см. в разделе [оператор Count](./countoperator.md).

## <a name="select-a-subset-of-columns-project"></a>Выбрать подмножество столбцов: *проект*

Используйте [проект](./projectoperator.md) , чтобы выбрать только нужные столбцы. См. Следующий пример, в котором используются как [проект](./projectoperator.md) , так и оператор [Take](./takeoperator.md) .

## <a name="filter-by-boolean-expression-where"></a>Фильтровать по логическому выражению: *где*

Давайте видим только `flood` события в `California` в февраля-2007:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

Выходные данные будут выглядеть следующим образом.

|StartTime|EndTime|Состояние|EventType|еписоденарративе|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Калифорния|Наводнение|Фронтальная система, перемещающаяся через Южный Сан-Жоакуин, применяет краткие периоды высокой дождяи к западноевропейскому округу в первые утром часов 19. Незначительное переполнение было сообщено в состоянии "незначительные" 166 рядом с Тафт.|

## <a name="show-n-rows-take"></a>Показывать *n* строк: *Take*

Давайте взглянем на некоторые данные. Что такое случайная выборка из пяти строк?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

Выходные данные будут выглядеть следующим образом.

|StartTime|EndTime|EventType|Состояние|Описание события (EventNarrative)|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Тяжелая дождя|Флорида|До 9 дюймов дождя за 24-часовой период между частями Коастал Волусиа округ.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Торнадо|Флорида|В торнадое Еустис на северном конце Западная Крукед Lake. Торнадо быстро переключается на EF1 надежность при перемещении Севера Северо-Запад через Еустис. Эта запись была находилась в течение двух миль и имеет максимальную ширину 300 метра.  Торнадо удалили 7 домов. Двадцать семи домов получили серьезные повреждения и 81 домов сообщили о незначительном повреждении. В $6 200 000 не было задано серьезного травмах, а свойство повреждено.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|ватерспаут|АТЛАНТИЧЕСКОЕ ЮГО|Ватерспаут, образованный в Атлантического Юго-Восточной Пляжее Мельбурн и ненадолго перемещается ближе к укрепление.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Шквалистый ветер|Миссисипи|Многочисленные крупные деревья оказались небольшими по линии электропитания. Возникли повреждения в Восточном Адамс округ.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Шквалистый ветер|Грузия|Диспетчеризация района сообщила о нескольких деревьях, а Куинцеи Баттен цикл почти в дороге 206. Оценка стоимости удаления дерева была оценена.|

Но [выводит](./takeoperator.md) строки из таблицы без определенного порядка, поэтому давайте отсортовать их. ([ограничение](./takeoperator.md) является псевдонимом для [Take](./takeoperator.md) и имеет тот же результат.)

## <a name="order-results-sort-top"></a>Порядок результатов: *Сортировка*, *Начало*

* *Примечание синтаксиса*. Некоторые операторы имеют параметры, которые вводятся с помощью ключевых слов, таких как `by` .
* В следующем примере `desc` заказы выводятся в порядке убывания и `asc` упорядочиваются по возрастанию.

Показать первые *n* строк, упорядоченных по определенному столбцу:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

Выходные данные будут выглядеть следующим образом.

|StartTime|EndTime|EventType|Состояние|Описание события (EventNarrative)|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зима|Мичиган|Это интенсивное событие снег, которое продолжается в первые утром часов в течение нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зима|Мичиган|Это интенсивное событие снег, которое продолжается в первые утром часов в течение нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зима|Мичиган|Это интенсивное событие снег, которое продолжается в первые утром часов в течение нового года.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Высокая обмотка|Калифорния|С севера до северо подойдет к концу густинг около 58 миль/ч было сообщено в горы округа Вентура.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Высокая обмотка|Калифорния|Датчик РАВСа "горячий пружины" сообщил о норсерли подойдет к концу густинг до 58 миль/ч.|

Тот же результат можно получить с помощью [сортировки](./sortoperator.md), а затем [выполнить](./takeoperator.md)следующие действия.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>Вычислить производные столбцы: *расширение*

Создание нового столбца путем вычисления значения в каждой строке:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

Выходные данные будут выглядеть следующим образом.

|StartTime|EndTime|Длительность|EventType|Состояние|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Тяжелая дождя|Флорида|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Торнадо|Флорида|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|ватерспаут|АТЛАНТИЧЕСКОЕ ЮГО|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Шквалистый ветер|Миссисипи|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Шквалистый ветер|Грузия|

Можно повторно использовать имя столбца и присвоить результат вычисления тому же столбцу.

Пример.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

Выходные данные будут выглядеть следующим образом.

|x|y|
|---|---|
|3|1|

В [скалярные выражения](./scalar-data-types/index.md) могут входить все обычные операторы `+` ( `-` ,,,, `*` `/` `%` ), а также доступен ряд полезных функций.

## <a name="aggregate-groups-of-rows-summarize"></a>Агрегатные группы строк: *итоговые данные*

Подсчитайте количество событий в каждой стране:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[суммирует](./summarizeoperator.md) группы строк с одинаковыми значениями в `by` предложении, а затем использует статистическую функцию (например, `count` ) для объединения каждой группы в одну строку. В этом случае существует строка для каждого состояния и столбец для количества строк в этом состоянии.

Доступен ряд [статистических функций](./summarizeoperator.md#list-of-aggregation-functions) . Для создания нескольких вычисленных столбцов можно использовать несколько агрегатных функций в одном `summarize` операторе. Например, можно получить число разрядов в каждом состоянии, а также сумму уникальных типов для каждого состояния. Затем мы можем использовать для получения наиболее [частых](./topoperator.md) состояний:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Выходные данные будут выглядеть следующим образом.

|Состояние|стормкаунт|типеофстормс|
|---|---|---|
|Техас|4701|27|
|Канзас|3166|21|
|Айова|2337|19|
|Иллинойс|2022|23|
|Миссури|2016|20|

В результатах `summarize` оператора:

* Каждому столбцу присваивается имя в `by` .
* Каждое вычисляемое выражение содержит столбец.
* Каждое сочетание `by` значений имеет строку.

## <a name="summarize-by-scalar-values"></a>Суммирование по скалярным значениям

В предложении можно использовать скалярные значения (числовые, временные или интервалы) `by` , но эти значения нужно поместить в ячейки с помощью функции [bin ()](./binfunction.md) :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Запрос сокращает все отметки времени до интервала в один день:

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[Bin ()](./binfunction.md) аналогична функции [floor ()](./floorfunction.md) на многих языках. Он просто сокращает каждое значение до ближайшего числа, кратного предоставленному модулю, чтобы [Сводные данные](./summarizeoperator.md) могли назначать строки группам.

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>Отображение диаграммы или таблицы: *рендеринг*

Можно проецировать два столбца и использовать их в качестве оси x и оси y диаграммы:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Снимок экрана, на котором показана гистограмма счетчика событий с различными состояниями.":::

Хотя мы удалили `mid` `project` операцию, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, `render` — это функция клиента, а не часть языка запросов. Тем не менее, она интегрирована в язык и полезна для представления результатов.


## <a name="timecharts"></a>Временные диаграммы

Вернувшись к числовым ячейкам, давайте выберем временные ряды:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Снимок экрана: График событий, binned по времени.":::

## <a name="multiple-series"></a>Множественные серии

Используйте несколько значений в предложении `summarize by` , чтобы создать отдельную строку для каждого сочетания значений.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Снимок экрана, показывающий количество таблиц по источнику.":::

Просто добавьте `render` термин в предыдущий пример: `| render timechart` .

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Снимок экрана, на котором показано число графиков по источнику.":::

Обратите внимание, что `render timechart` использует первый столбец в качестве оси x, а затем отображает другие столбцы в виде отдельных строк.

## <a name="daily-average-cycle"></a>Ежедневный средний цикл

Как действие зависит от среднего дня?

Подсчет событий по модулю времени один день, binned в час. Здесь мы используем `floor` вместо `bin` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Снимок экрана, на котором показано число диаграмму по часам.":::

В настоящее время `render` не имеет правильной метки длительности, но `| render columnchart` вместо этого можно использовать:

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Снимок экрана, показывающий число столбцов в гистограмме по часам.":::

## <a name="compare-multiple-daily-series"></a>Сравнение серий для нескольких дней

Как действие зависит от времени суток в различных штатах?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Снимок экрана диаграмму по часам и штату.":::

Выполните деление `1h` на, чтобы превратить ось x в число часов, а не на длительность:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Снимок экрана, показывающий гистограмму по часам и состоянию.":::

## <a name="join-data-types"></a>Объединение типов данных

Как можно найти два конкретного типа событий и в каком состоянии они потребуются?

Можно запрашивать события с помощью первого `EventType` и второго `EventType` , а затем объединить два набора в `State` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="Снимок экрана, показывающий присоединение к событиям молнии и множество.":::

## <a name="user-session-example-of-join"></a>Пример сеанса пользователя с *присоединением*

Этот раздел не использует `StormEvents` таблицу.

Предположим, что имеются данные, содержащие события, которые отмечают начало и конец каждого сеанса пользователя уникальным ИДЕНТИФИКАТОРом для каждого сеанса. 

Как узнать, сколько времени занимает каждый сеанс пользователя?

Можно использовать `extend` для предоставления псевдонима для двух меток времени, а затем вычислить длительность сеанса:

<!-- csl: https://help.kusto.windows.net/Samples -->
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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Снимок экрана: таблица результатов для расширения сеанса пользователя.":::

Рекомендуется использовать `project` для выбора только нужных столбцов перед выполнением объединения. В тех же предложениях переименуйте `timestamp` столбец.

## <a name="plot-a-distribution"></a>Построение графика распределения

Возвращаете `StormEvents` таблицу, сколько различий имеет разную длину?

<!-- csl: https://help.kusto.windows.net/Samples -->
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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Снимок экрана диаграмму результатов для счетчика событий по продолжительности.":::

Также можно использовать `| render columnchart` :

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Снимок экрана гистограммы для счетчика событий диаграмму по продолжительности.":::

## <a name="percentiles"></a>Процентили

Какие диапазоны длительности находятся в разных процентах.

Чтобы получить эти сведения, используйте предыдущий запрос, но замените `render` на:

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

В этом случае мы не использовали `by` предложение, поэтому выходные данные представляют собой одну строку:

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="Снимок экрана с таблицей результатов для суммирования процентили по продолжительности.":::

Из выходных данных можно увидеть следующее:

* 5% значений имеют длительность менее 5 минут.
* 50% последней менее одного часа и 25 минут.
* 5% последнейов составляет не менее двух часов и 50 минут.

Чтобы получить отдельную декомпозицию для каждого состояния, используйте `state` столбец отдельно с обоими `summarize` операторами:

<!-- csl: https://help.kusto.windows.net/Samples -->
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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Таблица сводка длительности процентили по состоянию.":::

## <a name="assign-a-result-to-a-variable-let"></a>Присвоить результат переменной: *let*

Используйте [let](./letstatement.md) , чтобы отделить части выражения запроса в предыдущем `join` примере. Результаты не изменились:

<!-- csl: https://help.kusto.windows.net/Samples -->
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
> [!TIP]
> Чтобы выполнить весь запрос в обозревателе Kusto, не добавляйте пустые строки между частями запроса.

## <a name="combine-data-from-several-databases-in-a-query"></a>Объединение данных из нескольких баз данных в запросе

В следующем запросе `Logs` таблица должна находиться в базе данных по умолчанию:

```kusto
Logs | where ...
```

Для доступа к таблице в другой базе данных используйте следующий синтаксис:

```kusto
database("db").Table
```

Например, если имеются базы данных с именем `Diagnostics` и необходимо `Telemetry` сопоставить некоторые данные в двух таблицах, можно использовать следующий запрос (предполагается, что `Diagnostics` это база данных по умолчанию):

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

Используйте этот запрос, если база данных по умолчанию `Telemetry` :

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
В предыдущих двух запросах предполагается, что обе базы данных находятся в кластере, к которому вы подключены в данный момент. Если `Telemetry` база данных находилась в кластере с именем *TelemetryCluster.kusto.Windows.NET*, то для доступа к ней используйте следующий запрос:

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Если кластер указан, база данных является обязательной.

Дополнительные сведения о объединении данных из нескольких баз данных в запросе см. в разделе [межбазовые запросы](cross-cluster-or-database-queries.md).

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры кода для [языка запросов Kusto](samples.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Лучший способ узнать о языке запросов Kusto — просмотреть некоторые базовые запросы, чтобы получить «впечатление» для языка. Эти запросы похожи на запросы, которые используются в руководстве по обозреватель данных Azure, но вместо этого они используют данные из общих таблиц в рабочей области Log Analytics Azure. 

Выполните эти запросы с помощью Log Analytics в портал Azure. Log Analytics — это средство, которое можно использовать для написания запросов журнала. Используйте данные журнала в Azure Monitor, а затем оцените результаты запроса журнала. Если вы не знакомы с Log Analytics, выполните инструкции [log Analytics руководстве](/azure/azure-monitor/log-query/log-analytics-tutorial).

Все запросы в этом учебнике используют [log Analytics демонстрационную среду](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Можно использовать собственную среду, но некоторые из таблиц могут не использоваться. Поскольку данные в демонстрационной среде не являются статическими, результаты запросов могут немного отличаться от показанных здесь результатов.


## <a name="count-rows"></a>Считать строки

Таблица [инсигхтсметрикс](/azure/azure-monitor/reference/tables/insightsmetrics) содержит данные о производительности, собираемые аналитическими сведениями, такими как Azure Monitor для виртуальных машин и Azure Monitor для контейнеров. Чтобы узнать, насколько велика таблица, мы будем передавать ее содержимое в оператор, который просто подсчитывает количество строк.

Запрос — это источник данных (обычно имя таблицы), за которым может следовать одна или несколько пар символа вертикальной черты и некоторый табличный оператор. В этом случае возвращаются все записи из `InsightsMetrics` таблицы, а затем они отправляются [оператору Count](./countoperator.md). `count`Оператор отображает результаты, так как оператор является последней командой в запросе.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

Выходные данные будут выглядеть следующим образом.

|Счетчик|
|-----|
|1 263 191|
    

## <a name="filter-by-boolean-expression-where"></a>Фильтровать по логическому выражению: *где*

Таблица [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) содержит записи из журнала действий Azure, которые предоставляют сведения о событиях уровня подписки или уровня группы управления, произошедших в Azure. Давайте видим только `Critical` записи в течение определенной недели.

Оператор [WHERE](/azure/data-explorer/kusto/query/whereoperator) является общим в языке запросов Kusto. `where` фильтрует таблицу в строки, соответствующие указанным условиям. В следующем примере используется несколько команд. Сначала запрос получает все записи для таблицы. Затем он фильтрует данные только для тех записей, которые находятся в диапазоне времени. Наконец, он фильтрует результаты только для тех записей, которые имеют `Critical` уровень.

> [!NOTE]
> Помимо указания фильтра в запросе с помощью `TimeGenerated` столбца, можно указать диапазон времени в log Analytics. Дополнительные сведения см. [в разделе Журнал запросов и диапазон времени в Azure Monitor log Analytics](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора WHERE.":::

## <a name="select-a-subset-of-columns-project"></a>Выбрать подмножество столбцов: *проект*

Используйте [Project](./projectoperator.md) , чтобы включить только нужные столбцы. Основываясь на предыдущем примере, можно ограничить выходные данные определенными столбцами:

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="Снимок экрана, на котором показаны результаты выполнения примера оператора Project.":::

## <a name="show-n-rows-take"></a>Показывать *n* строк: *Take*

[Нетворкмониторинг](/azure/azure-monitor/reference/tables/networkmonitoring) содержит данные мониторинга для виртуальных сетей Azure. Давайте воспользуемся оператором [Take](./takeoperator.md) для просмотра пяти случайных строк выборки в этой таблице. При [этом](./takeoperator.md) выводится определенное число строк из таблицы без определенного порядка:

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора Take.":::

## <a name="order-results-sort-top"></a>Порядок результатов: *Сортировка*, *Начало*

Вместо случайных записей мы можем вернуть последние пять записей с помощью первой сортировки по времени:

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Это точное поведение можно получить, используя оператор [Top](./topoperator.md) : 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора TOP.":::

## <a name="compute-derived-columns-extend"></a>Вычислить производные столбцы: *расширение*

Оператор [Extend](./projectoperator.md) аналогичен [проекту](./projectoperator.md), но он добавляет к набору столбцов вместо того, чтобы заменять их. Для создания нового столбца на основе вычисления в каждой строке можно использовать оба оператора.

Таблица [производительности](/azure/azure-monitor/reference/tables/perf) содержит данные о производительности, собранные с виртуальных машин, на которых выполняется агент log Analytics. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора расширения.":::

## <a name="aggregate-groups-of-rows-summarize"></a>Агрегатные группы строк: *итоговые данные*

Оператор [суммирования](./summarizeoperator.md) группирует строки, имеющие одинаковые значения в `by` предложении. Затем она использует статистическую функцию, например `count` , для объединения каждой группы в одну строку. Доступен ряд [статистических функций](./summarizeoperator.md#list-of-aggregation-functions) . Для создания нескольких вычисленных столбцов можно использовать несколько агрегатных функций в одном `summarize` операторе. 

Таблица [SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) содержит такие события безопасности, как вход в систему и процессы, запущенные на наблюдаемых компьютерах. Вы можете подсчитать количество событий каждого уровня, произошедших на каждом компьютере. В этом примере для каждого сочетания компьютера и уровня создается строка. Столбец содержит количество событий.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора суммирования.":::

## <a name="summarize-by-scalar-values"></a>Суммирование по скалярным значениям

Можно выполнять статистическую обработку по скалярным значениям, таким как числа и значения времени, но для группирования строк в отдельные наборы данных следует использовать функцию [bin ()](./binfunction.md) . Например, если статистическое выражение выполняется по `TimeGenerated` , то для получения практически всех значений времени будет выводится строка. Используйте `bin()` для консолидации этих значений в час или день.

Таблица [инсигхтсметрикс](/azure/azure-monitor/reference/tables/insightsmetrics) содержит данные о производительности, собираемые аналитическими сведениями, такими как Azure Monitor для виртуальных машин и Azure Monitor для контейнеров. В следующем запросе показана Почасовая средняя загрузка процессора для нескольких компьютеров:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора AVG.":::

## <a name="display-a-chart-or-table-render"></a>Отображение диаграммы или таблицы: *рендеринг*

Оператор [Render](./renderoperator.md?pivots=azuremonitor) задает способ подготовки к просмотру выходных данных запроса. По умолчанию Log Analytics отображает выходные данные в виде таблицы. После выполнения запроса можно выбрать другие типы диаграмм. `render`Оператор полезен для включения в запросы, в которых обычно предпочтительнее использовать конкретный тип диаграммы.

В следующем примере показана Почасовая средняя загрузка процессора для одного компьютера. Он отображает выходные данные в виде диаграмму.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="Снимок экрана, на котором показаны результаты выполнения примера оператора Render.":::

## <a name="work-with-multiple-series"></a>Работа с несколькими рядами

При использовании нескольких значений в `summarize by` предложении на диаграмме отображаются отдельные ряды для каждого набора значений:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="Снимок экрана, на котором показаны результаты выполнения оператора Render с несколькими рядами.":::

## <a name="join-data-from-two-tables"></a>Объединение данных из двух таблиц

Что делать, если необходимо получить данные из двух таблиц в одном запросе? Оператор [Join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) можно использовать для объединения строк из нескольких таблиц в одном результирующем наборе. Каждая таблица должна иметь столбец с совпадающим значением, чтобы соединение собрало, какие строки следует сопоставить.

[Вмкомпутер](/azure/azure-monitor/reference/tables/vmcomputer) — это таблица, которая Azure Monitor используется для виртуальных машин для хранения сведений о виртуальных машинах, которые он отслеживает. [Инсигхтсметрикс](/azure/azure-monitor/reference/tables/insightsmetrics) содержит данные о производительности, собираемые из этих виртуальных машин. Одним из значений, собранных в *инсигхтсметрикс* , является доступная память, но не доступная процентная память. Чтобы вычислить процентную долю, требуется физическая память для каждой виртуальной машины. Это значение в `VMComputer` .

В следующем примере запроса для выполнения этого вычисления используется соединение. Оператор [DISTINCT](/azure/data-explorer/kusto/query/distinctoperator) используется вместе с `VMComputer` тем, что сведения регулярно собираются с каждого компьютера. В результате для каждого компьютера в таблице создается несколько строк. Две таблицы соединяются с помощью `Computer` столбца. В результирующем наборе создается строка, которая содержит столбцы из обеих таблиц для каждой строки в `InsightsMetrics` , со значением в соответствии с тем `Computer` же значением в `Computer` столбце в `VMComputer` .

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора Join.":::

## <a name="assign-a-result-to-a-variable-let"></a>Присвоить результат переменной: *let*

С помощью [let](./letstatement.md) можно упростить чтение и управление запросами. Этот оператор можно использовать для назначения результатов запроса переменной, которую можно использовать позднее. С помощью `let` инструкции запрос в предыдущем примере может быть переписан следующим образом:

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
    let AvailableMemory = 
InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="Снимок экрана, на котором показаны результаты примера оператора let.":::

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры кода для [языка запросов Kusto](samples.md?pivots=azuremonitor).


::: zone-end
