---
title: Руководство по запросам Kusto в Azure Data Explorer и Azure Monitor
description: В этом учебнике описывается, как выполнять запросы на языке запросов Kusto для соблюдения требований к запросам в Azure Data Explorer и Azure Monitor.
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
ms.openlocfilehash: d66b8d949e04f6cd672078989c9d047729c9596b
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360087"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>Руководство по использованию запросов Kusto в Azure Data Explorer и Azure Monitor

::: zone pivot="azuredataexplorer"

Лучший способ ознакомиться с языком запросов Kusto — просмотреть некоторые базовые запросы, чтобы лучше его понять. Рекомендуем [использовать базу данных с примерами данных](https://help.kusto.windows.net/Samples). Запросы, показанные в этом учебнике, должны выполняться в этой базе данных. Таблица `StormEvents` в примере базы данных содержит сведения о штормах, которые были в США.

## <a name="count-rows"></a>Считать строки

В примере базы данных есть таблица с именем `StormEvents`. Чтобы узнать размер таблицы, мы передаем ее содержимое в оператор, который просто подсчитывает строки в таблице. 

*Примечание по синтаксису.* Запрос — это источник данных (обычно имя таблицы), за которым может следовать одна или несколько пар символа вертикальной черты и табличного оператора.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

Выходные данные будут выглядеть следующим образом.

|Count|
|-----|
|59 066|
    
Дополнительные сведения см. в статье [Оператор count](./countoperator.md).

## <a name="select-a-subset-of-columns-project"></a>Выбор подмножества столбцов с помощью оператора *project*

С помощью оператора [project](./projectoperator.md) можно выбрать только нужные столбцы. См. следующий пример, в котором используются такие операторы, как [project](./projectoperator.md) и [take](./takeoperator.md).

## <a name="filter-by-boolean-expression-where"></a>Фильтрация по логическому выражению с помощью оператора *where*

Давайте просмотрим только события `flood` в `California` за февраль 2007 г.:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

Выходные данные будут выглядеть следующим образом.

|StartTime|EndTime|Состояние|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Калифорния|Наводнение|Фронтальная система, перемещающаяся через южную область долины Сан-Хоакин, принесла кратковременный сильный ливневый дождь в западную часть округа Керн ранним утром 19 числа. Сообщалось о незначительном наводнении вдоль внутриштатного шоссе 166 около Тафта.|

## <a name="show-n-rows-take"></a>Отображение *n* строк с помощью оператора *take*

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
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Сильный ливень|Флорида|За 24 часа в прибрежных районах округа Волуша выпало целых 9 дюймов осадков.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Ураган|Флорида|Ураган нагрянул в город Юстис в северном конце западного Крукед Лейк. Ураган быстро усилился до класса EF1, перемещаясь на северо-северо-запад через Юстис. Длина его пути составляла чуть меньше двух миль, а максимальная ширина — 300 ярдов.  Ураган разрушил 7 домов. Двадцать семь домов получили серьезные повреждения, а 81 дом — незначительные. Серьезных травм не было, материальный ущерб составил 6,2 млн долларов США.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Торнадо|ЮЖНО-АТЛАНТИЧЕСКИЕ ШТАТЫ|Торнадо образовался в Атлантическом океане к юго-востоку от Мелборн-Бич и быстро приблизился к берегу.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Шквалистый ветер|Миссисипи|Было повалено множество больших деревьев, некоторые из них упали на линии электропередач. Повреждения затронули восточную часть округа Адамс.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Шквалистый ветер|Грузия|По сообщениям диспетчера округа несколько деревьев были повалены ветром вдоль Квинси Баттен Луп возле дороги внутриштатной автодорожной сети 206. Были подсчитаны расходы на уборку деревьев.|

Но [выводит](./takeoperator.md) строки из таблицы без определенного порядка, поэтому давайте отсортовать их. ([limit](./takeoperator.md) является псевдонимом для оператора [take](./takeoperator.md) и дает такой же результат.)

## <a name="order-results-sort-top"></a>Упорядочение результатов с помощью операторов *sort* и *top*

* *Примечание по синтаксису.* У некоторых операторов есть параметры, которые представлены в виде ключевых слов, таких как `by`.
* В следующем примере `desc` упорядочивает результаты по убыванию, а `asc` — по возрастанию.

Показать первые *n* строк, упорядоченных по одному из столбцов:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

Выходные данные будут выглядеть следующим образом.

|StartTime|EndTime|EventType|Состояние|Описание события (EventNarrative)|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Метель|Мичиган|Этот сильный снегопад продолжался до раннего утра Нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Метель|Мичиган|Этот сильный снегопад продолжался до раннего утра Нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Метель|Мичиган|Этот сильный снегопад продолжался до раннего утра Нового года.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Очень сильный ветер|Калифорния|Сообщалось о порывах ветра с севера на северо-восток со скоростью около 58 миль/ч в горах округа Вентура.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Очень сильный ветер|Калифорния|Датчик метеорологической станции в Уорм-Спрингс зафиксировал порывы северного ветра до 58 миль/ч.|

Те же результаты можно получить, используя оператор [sort](./sortoperator.md), а затем [take](./takeoperator.md):

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>Вычисление производных столбцов с помощью оператора *extend*

Выполните следующий запрос, чтобы создать столбец путем вычисления значения в каждой строке:

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
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Сильный ливень|Флорида|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Ураган|Флорида|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Торнадо|ЮЖНО-АТЛАНТИЧЕСКИЕ ШТАТЫ|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Шквалистый ветер|Миссисипи|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Шквалистый ветер|Грузия|

Вы можете повторно использовать имя столбца и присвоить ему результаты вычисления.

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

[Скалярные выражения](./scalar-data-types/index.md) могут включать в себя все обычные операторы (`+`, `-`, `*`, `/`, `%`) и ряд полезных функций.

## <a name="aggregate-groups-of-rows-summarize"></a>Агрегирование групп строк с помощью оператора *summarize*

Подсчитайте количество событий для каждого штата:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

Оператор [summarize](./summarizeoperator.md) группирует строки, имеющие одинаковые значения, в предложении `by`, а затем использует статистическую функцию (например, `count`) для объединения каждой группы в одну строку. В этом случае у нас есть по одной строке для каждого штата и столбец с количеством строк в этом штате.

Доступен ряд [статистических функций](./summarizeoperator.md#list-of-aggregation-functions). Вы можете использовать некоторые статистические функции в одном операторе `summarize` для получения нескольких вычисляемых столбцов. Например, можно получить количество штормов в каждом штате, а также общее количество уникальных типов штормов для каждого штата. Затем можно использовать оператор [top](./topoperator.md), чтобы получить сведения о штатах, наиболее пострадавших от штормов:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Выходные данные будут выглядеть следующим образом.

|Состояние|StormCount|TypeOfStorms|
|---|---|---|
|Техас|4701|27|
|Канзас|3166|21|
|Айова|2337|19|
|Иллинойс|2022|23|
|Миссури|2016|20|

В результатах оператора `summarize`:

* каждому столбцу, присваивается имя в `by`;
* каждое вычисляемое выражение имеет столбец;
* каждое сочетания значений `by` имеет строку.

## <a name="summarize-by-scalar-values"></a>Суммирование по скалярным значениям

В предложении `by` можно использовать скалярные значения (числовые, временные или интервалы), но их необходимо поместить в ячейки с помощью функции [bin()](./binfunction.md):

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Запрос уменьшает все метки времени до интервала в один день:

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

Функция [bin ()](./binfunction.md) аналогична функции [floor()](./floorfunction.md) на различных языках. Она просто уменьшает каждое значение до ближайшего кратного указанного остатка, чтобы оператор [summarize](./summarizeoperator.md) мог назначить строки группам.

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>Отображение диаграммы или таблицы с помощью оператора *render*

Вы можете проецировать два столбца и использовать их в качестве осей X и Y диаграммы:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Снимок экрана: гистограмма с количеством штормов по штату.":::

Хотя мы удалили элемент `mid` в операции `project`, он нам еще пригодится, если мы хотим, чтобы штаты на диаграмме отображались именно в таком порядке.

Строго говоря, `render` — это клиентская функция, а не часть языка запросов. Тем не менее, она интегрирована в язык и полезна для представления результатов.


## <a name="timecharts"></a>Временные диаграммы

Возвращаясь к числовым ячейкам, давайте отобразим временные ряды:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Снимок экрана: график событий, сегментированных по времени.":::

## <a name="multiple-series"></a>Множественные серии

Используйте несколько значений в предложении `summarize by` , чтобы создать отдельную строку для каждого сочетания значений.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Снимок экрана: подсчет событий в таблице с разбивкой по источнику.":::

Просто добавьте термин `render` к предыдущему примеру: `| render timechart`.

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Снимок экрана: подсчет событий на графике с разбивкой по источнику.":::

Обратите внимание, что `render timechart` использует первый столбец в качестве оси X, а затем отображает другие столбцы в виде отдельных линий.

## <a name="daily-average-cycle"></a>Ежедневный средний цикл

Как изменяется активность в течение обычного дня?

Количество событий в зависимости от времени по модулю в один день, сгруппированное по часам. Здесь вместо значения `bin` используется `floor`:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Снимок экрана: подсчет событий на временной диаграмме с разбивкой по часам.":::

В настоящее время `render` не устанавливает должным образом метки для продолжительности, но вместо этого можно использовать `| render columnchart`:

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Снимок экрана: подсчет на гистограмме с разбивкой по часам.":::

## <a name="compare-multiple-daily-series"></a>Сравнение серий для нескольких дней

Как изменяется активность в течение дня в различных штатах?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Снимок экрана: временная диаграмма с разбивкой по часам и штатам.":::

Выполните разделение на `1h`, чтобы на оси X отображалось количество часов, а не длительность:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Снимок экрана: гистограмма с разбивкой по часам и штатам.":::

## <a name="join-data-types"></a>Объединение типов данных

Как можно найти два конкретных типа события и штат, в котором они произошли?

Вы можете извлечь события, связанные со штормом, с помощью первого события `EventType` и второго события `EventType`, а затем объединить два набора в столбце `State`:

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="Снимок экрана: объединение событий, связанных с молнией и лавиной.":::

## <a name="user-session-example-of-join"></a>Пример сеанса пользователя с использованием оператора *join*

В этом разделе не используется таблица `StormEvents`.

Предположим, что имеются данные, содержащие события, отмечающие начало и конец каждого сеанса пользователя уникальным идентификатором для каждого сеанса. 

Как узнать, сколько времени длится каждый сеанс пользователя?

Вы можете использовать `extend`, чтобы указать псевдоним для двух меток времени, а затем вычислить длительность сеанса:

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Снимок экрана: таблица результатов для сеанса пользователя с расширением.":::

Рекомендуется использовать `project`, чтобы перед выполнением объединения выбрать только необходимые столбцы. В тех же предложениях переименуйте столбец `timestamp`.

## <a name="plot-a-distribution"></a>Построение графика распределения

Возвращаясь к таблице `StormEvents`, какое количество штормов различной длительности существует?

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Снимок экрана: временная диаграмма с количеством событий с разбивкой по продолжительности.":::

Вы также можете использовать `| render columnchart`:

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Снимок экрана: гистограмма для временной диаграммы подсчета событий с разбивкой по продолжительности.":::

## <a name="percentiles"></a>Процентили

Какие диапазоны длительности находятся в разных процентных долях штормов?

Чтобы получить эти сведения, используйте предыдущий запрос, но замените `render` следующим значением:

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

В этом случае мы не использовали предложение `by`, поэтому выходные данные представляют собой одну строку:

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="Снимок экрана: таблица результатов для суммирования процентилей с разбивкой по продолжительности.":::

На основе выходных данных можно определить следующее:

* 5 % штормов имеют продолжительность менее 5 минут;
* 50 % штормов длились менее одного часа и 25 минут;
* 95 % штормов длились менее двух часов и 50 минут.

Чтобы получить отдельную разбивку для каждого штата, используйте столбец `state` отдельно с обеими операторами `summarize`:

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Таблица со сводкой длительности процентилей с разбивкой по штатам.":::

## <a name="assign-a-result-to-a-variable-let"></a>Присвоение результата переменной с помощью оператора *let*

С помощью оператора [let](./letstatement.md) можно разделить части выражения запроса в предыдущем примере `join`. Результаты не изменились:

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

В следующем запросе таблица `Logs` должна находиться в базе данных по умолчанию:

```kusto
Logs | where ...
```

Для доступа к таблице в другой базе данных используйте следующий синтаксис:

```kusto
database("db").Table
```

Например, если имеются базы данных с именами `Diagnostics` и `Telemetry` и в них необходимо сопоставить некоторые данные, можно использовать следующий запрос (предполагается, что `Diagnostics` является базой данных по умолчанию):

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

Используйте этот запрос, если базой данных по умолчанию является `Telemetry`:

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
В предыдущих двух запросах предполагается, что обе базы данных находятся в кластере, к которому вы подключены в данный момент. Если база данных `Telemetry` находилась в кластере с именем *TelemetryCluster.kusto.windows.net*, то для доступа к ней используйте следующий запрос:

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Если указан кластер, то определение базы данных является обязательной.

Дополнительные сведения об объединении данных из нескольких баз данных в запросе см. в [этой статье](cross-cluster-or-database-queries.md).

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры кода для [языка запросов Kusto](samples.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Лучший способ ознакомиться с языком запросов Kusto — просмотреть некоторые базовые запросы, чтобы лучше его понять. Эти запросы аналогичны запросам, которые используются в учебнике по Azure Data Explorer, однако в них используются данные из общих таблиц в рабочей области Azure Log Analytics. 

Выполните эти запросы с помощью службы Log Analytics на портале Azure. Log Analytics — это средство, которое можно использовать для написания запросов журнала. Используйте данные журнала в Azure Monitor, а затем оцените результаты запроса журнала. Если вы не знакомы с Log Analytics, просмотрите учебник по [Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial).

Все запросы в этом учебнике используют [демонстрационную среду Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Вы можете использовать собственную среду, однако вам будут недоступны некоторые таблицы, приведенные в этом учебнике. Так как данные в демонстрационной среде не являются статическими, результаты ваших запросов могут немного отличаться от показанных здесь.


## <a name="count-rows"></a>Считать строки

Таблица [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) содержит данные производительности, собираемые службами аналитики, такими как Azure Monitor для виртуальных машин и Azure Monitor для контейнеров. Чтобы узнать размер таблицы, мы передаем ее содержимое в оператор, который просто подсчитывает строки.

Запрос — это источник данных (обычно имя таблицы), за которым может следовать одна или несколько пар символа вертикальной черты и табличного оператора. В этом случае возвращаются все записи из таблицы `InsightsMetrics`, а затем отправляются [оператору count](./countoperator.md). Оператор `count` отображает результаты, так как он является последней командой в запросе.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

Выходные данные будут выглядеть следующим образом.

|Count|
|-----|
|1 263 191|
    

## <a name="filter-by-boolean-expression-where"></a>Фильтрация по логическому выражению с помощью оператора *where*

Таблица [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) содержит записи из журнала действий Azure с аналитическими сведениями о событиях на уровне подписки или на уровне группы управления, которые произошли в Azure. Давайте рассмотрим только записи `Critical` за определенную неделю.

Оператор [where](./whereoperator.md) является общепринятым в языке запросов Kusto. `where` фильтрует таблицу по строкам, соответствующим указанным условиям. В следующем примере используется несколько команд. Сначала, запрос извлекает все записи для таблицы. Затем фильтрует эти данные только по записям, которые соответствуют диапазону времени. Наконец, он фильтрует эти результаты только по записям, которые имеют уровень `Critical`.

> [!NOTE]
> Помимо указания фильтра с помощью столбца `TimeGenerated` в запросе можно указать диапазон времени в Log Analytics. Подробные сведения см. в статье [Область запросов журнала и временной диапазон в Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="Снимок экрана: результаты примера с оператором where.":::

## <a name="select-a-subset-of-columns-project"></a>Выбор подмножества столбцов с помощью оператора *project*

С помощью оператора [project](./projectoperator.md) можно выбрать только нужные столбцы. Основываясь на предыдущем примере, давайте ограничим выходные данные определенными столбцами:

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="Снимок экрана: результаты примера с оператором project.":::

## <a name="show-n-rows-take"></a>Отображение *n* строк с помощью оператора *take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) содержит данные мониторинга для виртуальных сетей Azure. Давайте воспользуемся оператором [take](./takeoperator.md) и просмотрим десять случайных строк из этой таблицы. Оператор [take](./takeoperator.md) показывает заданное количество строк из таблицы без упорядочения:

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="Снимок экрана: результаты примера с оператором take.":::

## <a name="order-results-sort-top"></a>Упорядочение результатов с помощью операторов *sort* и *top*

Вместо случайных записей мы можем вернуть последние пять записей, отсортировав их сначала по времени:

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Этот же результат можно получить, используя оператор [top](./topoperator.md): 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="Снимок экрана: результаты примера с оператором top.":::

## <a name="compute-derived-columns-extend"></a>Вычисление производных столбцов с помощью оператора *extend*

Оператор [extend](./projectoperator.md) аналогичен оператору [project](./projectoperator.md), но он добавляет значение к набору столбцов, а не заменяет их. Для создания столбца на основе вычисления в каждой строке можно использовать оба оператора.

Таблица [Perf](/azure/azure-monitor/reference/tables/perf) содержит данные производительности, собранные с виртуальных машин, на которых выполняется агент Log Analytics. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="Снимок экрана: результаты примера с оператором extend.":::

## <a name="aggregate-groups-of-rows-summarize"></a>Агрегирование групп строк с помощью оператора *summarize*

Оператор [summarize](./summarizeoperator.md) группирует строки, имеющие одинаковые значения в предложении `by`. Затем для объединения каждой группы в одну строку он использует статистическую функцию, такую как `count`. Доступен ряд [статистических функций](./summarizeoperator.md#list-of-aggregation-functions). Вы можете использовать некоторые статистические функции в одном операторе `summarize` для получения нескольких вычисляемых столбцов. 

Таблица [SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) содержит такие события безопасности, как вход в систему и процессы, запущенные на отслеживаемых компьютерах. Вы можете подсчитать количество событий каждого уровня для каждого компьютера. В этом примере для каждого сочетания компьютера и уровня создается строка. Столбец содержит количество событий.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="Снимок экрана: результаты примера с оператором summarize count.":::

## <a name="summarize-by-scalar-values"></a>Суммирование по скалярным значениям

Вы можете выполнять статистическую обработку по скалярным значениям, таким как числа и значения времени, но для группирования строк в отдельные наборы данных следует использовать функцию [bin()](./binfunction.md). Например, если агрегирование выполняется по `TimeGenerated`, вы получите строку почти для каждого значения времени. Используйте `bin()` для консолидации этих значений по часу или дню.

Таблица [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) содержит данные производительности, собираемые службами аналитики, такими как Azure Monitor для виртуальных машин и Azure Monitor для контейнеров. В следующем запросе показана почасовая средняя загрузка процессора для нескольких компьютеров:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="Снимок экрана: результаты примера с оператором avg.":::

## <a name="display-a-chart-or-table-render"></a>Отображение диаграммы или таблицы с помощью оператора *render*

Оператор [Render](./renderoperator.md?pivots=azuremonitor) указывает, как преобразовывать выходные данные запроса для просмотра. По умолчанию Log Analytics преобразовывает выходные данные для просмотра в виде таблицы. После выполнения запроса можно выбрать разные стили диаграмм. Оператор `render` рекомендуется включить в запросы, в которых обычно предпочтительнее использовать определенный стиль диаграммы.

В следующем примере показана почасовая средняя загрузка процессора для одного компьютера. Он преобразует выходные данные для просмотра в виде временной диаграммы.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="Снимок экрана: результаты примера с оператором render.":::

## <a name="work-with-multiple-series"></a>Использование нескольких рядов

Если используете несколько значений в предложении `summarize by`, то для каждого набора значений на диаграмме отображается отдельный ряд:

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="Снимок экрана: результаты примера с оператором render с несколькими рядами.":::

## <a name="join-data-from-two-tables"></a>Объединение данных из двух таблиц

Что делать, если необходимо получить данные из двух таблиц в одном запросе? Оператор [join](./joinoperator.md?pivots=azuremonitor) можно использовать для объединения строк из нескольких таблиц в одном результирующем наборе. В каждой таблице должен быть столбец с соответствующим значением, чтобы оператор join понимал, какие строки следует сопоставить.

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) — это таблица, которую Azure Monitor использует для виртуальных машин, чтобы хранить сведения о виртуальных машинах, которые он отслеживает. Таблица [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) содержит данные производительности, собираемые с этих виртуальных машин. В таблице *InsightsMetrics* содержится значение доступной памяти (но не ее процент). Чтобы вычислить процент, требуются данные о физической памяти для каждой виртуальной машины. Это значение находится в таблице `VMComputer`.

В следующем примере запроса для выполнения этого вычисления используется оператор join. Оператор [distinct](./distinctoperator.md) используется с `VMComputer`, так как сведения регулярно собираются с каждого компьютера. В результате для каждого компьютера в таблице создается несколько записей. Две таблицы объединяются с помощью столбца `Computer`. В результирующем наборе создается запись, которая включает столбцы из обеих таблиц для каждой записи в таблице `InsightsMetrics` со значением в столбце `Computer`, которое соответствует такому же значению в столбце `Computer` таблицы `VMComputer`.

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

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="Снимок экрана: результаты примера с оператором join.":::

## <a name="assign-a-result-to-a-variable-let"></a>Присвоение результата переменной с помощью оператора *let*

Используйте оператор [let](./letstatement.md), чтобы упростить чтение и управление запросами. Этот оператор можно использовать для присвоения результатов запроса переменной, которую можно использовать позднее. С помощью оператора `let` запрос в предыдущем примере можно переписать следующим образом:

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
let AvailableMemory = InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="Снимок экрана: результаты примера с оператором let.":::

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите примеры кода для [языка запросов Kusto](samples.md?pivots=azuremonitor).


::: zone-end
