---
title: Руководство по Azure обозреватель данных
description: В этой статье описывается руководство по Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 64736d944c71d84c4950dea0341089732b258f27
ms.sourcegitcommit: a10e7c6ba96bdb94d95ef23f5d1506eb8fda0041
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058688"
---
# <a name="tutorial"></a>Учебник

::: zone pivot="azuredataexplorer"

Лучший способ узнать о языке запросов Kusto — просмотреть некоторые простые запросы, чтобы получить "впечатление" для языка, использующего [базу данных с некоторыми примерами данных](https://help.kusto.windows.net/Samples). Запросы, показанные в этой статье, должны выполняться в этой базе данных. `StormEvents`Таблица в этом образце базы данных содержит некоторые сведения о выделах, произошедших в США.

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>Считать строки

В нашем примере базы данных имеется таблица с именем `StormEvents` .
Чтобы узнать, насколько это было, мы будем передавать его содержимое в оператор, который просто подсчитывает строки:

* *Синтаксис:* Запрос — это источник данных (обычно имя таблицы), за которым может следовать одна или несколько пар символа вертикальной черты и некоторый табличный оператор.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

Ниже приведен результат:

|Count|
|-----|
|59066|
    
[оператор Count](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>Проект: выберите подмножество столбцов

Используйте [проект](./projectoperator.md) , чтобы выбрать только нужные столбцы. См. пример ниже, в котором используются оба [проекта](./projectoperator.md) и оператор [Take](./takeoperator.md) .

## <a name="where-filtering-by-a-boolean-expression"></a>WHERE: Фильтрация по логическому выражению

Давайте посмотрим только `flood` s в в `California` течение февраля-2007:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|Состояние|EventType|еписоденарративе|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Калифорния|Наводнение|Фронтальная система, перемещающаяся через Южный Сан-Жоакуин, применяет краткие периоды высокой дождяи к западноевропейскому округу в первые утром часов 19. Незначительное переполнение было сообщено в состоянии "незначительные" 166 рядом с Тафт.|

## <a name="take-show-me-n-rows"></a>Возьмем: Показать n строк

Давайте просмотрим некоторые данные — что содержится в выборке из 5 строк?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|Состояние|Описание события (EventNarrative)|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Тяжелая дождя|Флорида|До 9 дюймов дождя за 24-часовой период между частями Коастал Волусиа округ.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Торнадо|Флорида|В торнадое Еустис на северном конце Западная Крукед Lake. Торнадо быстро переключается на EF1 надежность при перемещении Севера Северо-Запад через Еустис. Эта запись была находилась в течение двух миль и имеет максимальную ширину 300 метра.  Торнадо удалили 7 домов. Двадцать семи домов получили серьезные повреждения и 81 домов сообщили о незначительном повреждении. В $6 200 000 не было задано серьезного травмах, а свойство повреждено.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|ватерспаут|АТЛАНТИЧЕСКОЕ ЮГО|Ватерспаут, образованный в Атлантического Юго-Восточной Пляжее Мельбурн и ненадолго перемещается ближе к укрепление.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Шквалистый ветер|Миссисипи|Многочисленные крупные деревья оказались небольшими по линии электропитания. Возникли повреждения в Восточном Адамс округ.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Шквалистый ветер|Грузия|Диспетчеризация района сообщила о нескольких деревьях, а Куинцеи Баттен цикл почти в дороге 206. Оценка стоимости удаления дерева была оценена.|

Но [выводит](./takeoperator.md) строки из таблицы без определенного порядка, поэтому давайте отсортовать их.
* [Limit](./takeoperator.md) — это псевдоним для [Take](./takeoperator.md) и будет иметь тот же результат.

## <a name="sort-and-top"></a>Сортировка и начало

* *Синтаксис:* Некоторые операторы имеют параметры, представленные ключевыми словами, такими как `by` .
* `desc` — по убыванию, `asc` — по возрастанию.

Показать первые n строк, упорядоченных по одному из столбцов:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|Состояние|Описание события (EventNarrative)|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зима|Мичиган|Это интенсивное событие снег, которое продолжается в первые утром часов в течение нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зима|Мичиган|Это интенсивное событие снег, которое продолжается в первые утром часов в течение нового года.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Зима|Мичиган|Это интенсивное событие снег, которое продолжается в первые утром часов в течение нового года.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Высокая обмотка|Калифорния|С севера до северо подойдет к концу густинг около 58 миль/ч было сообщено в горы округа Вентура.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Высокая обмотка|Калифорния|Датчик РАВСа "горячий пружины" сообщил о норсерли подойдет к концу густинг до 58 миль/ч.|

То же самое можно сделать с помощью функции [Sort](./sortoperator.md) , а затем [взять](./takeoperator.md) оператор

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>расширение: вычисление производных столбцов

Создание нового столбца путем вычисления значения в каждой строке:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Длительность|EventType|Состояние|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Тяжелая дождя|Флорида|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Торнадо|Флорида|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|ватерспаут|АТЛАНТИЧЕСКОЕ ЮГО|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Шквалистый ветер|Миссисипи|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Шквалистый ветер|Грузия|

Можно повторно использовать имя столбца и присвоить результат вычисления одному и тому же столбцу.
Например.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[Скалярные выражения](./scalar-data-types/index.md) могут включать все обычные операторы ( `+` , `-` , `*` , `/` , `%` ), а также ряд полезных функций.

## <a name="summarize-aggregate-groups-of-rows"></a>суммировать: статистические группы строк

Подсчет количества событий, поступающих из каждой страны:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[суммирует](./summarizeoperator.md) группы строк с одинаковыми значениями в `by` предложении, а затем использует статистическую функцию (например, `count` ) для объединения каждой группы в одну строку. Поэтому в этом случае есть строка для каждого состояния и столбец для числа строк в этом состоянии.

Существует ряд [статистических функций](./summarizeoperator.md#list-of-aggregation-functions), которые можно использовать в одном операторе суммирования для создания нескольких вычисленных столбцов. Например, можно получить число разрядов в каждом состоянии, а также сумму уникальных типов для каждого состояния,  
Затем мы можем использовать Top для получения наиболее [частых](./topoperator.md) состояний:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|Состояние|стормкаунт|типеофстормс|
|---|---|---|
|Техас|4701|27|
|Канзас|3166|21|
|Айова|2337|19|
|Иллинойс|2022|23|
|Миссури|2016|20|

Результат суммирования имеет:

* каждый столбец, указанный в `by`;
* столбец для каждого вычисляемого выражения;
* строку для каждого сочетания значений `by` .

## <a name="summarize-by-scalar-values"></a>Суммирование по скалярным значениям

В предложении можно использовать скалярные значения (числовые, временные или интервалы) `by` , но значения необходимо поместить в ячейки.  
Функция [bin ()](./binfunction.md) полезна для этого:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Это сокращает время отметок времени до интервала в 1 день:

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

## <a name="render-display-a-chart-or-table"></a>Render: отображение диаграммы или таблицы

Проект два столбца и использование их в качестве оси x и y диаграммы:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render" — это функция клиента, а не часть языка запросов. Тем не менее, она интегрирована в язык и очень полезна для представления результатов.


## <a name="timecharts"></a>Временные диаграммы

Вернувшись к числовым ячейкам, давайте выберем временные ряды:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

## <a name="multiple-series"></a>Множественные серии

Используйте несколько значений в предложении `summarize by` , чтобы создать отдельную строку для каждого сочетания значений.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

Просто добавьте термин прорисовки к приведенной выше: `| render timechart` .

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

Обратите внимание, что `render timechart` использует первый столбец в качестве оси x, а затем отображает другие столбцы в виде отдельных строк.

## <a name="daily-average-cycle"></a>Ежедневный средний цикл

Как действие зависит от среднего дня?

Подсчет событий по модулю времени один день, binned в час. Обратите внимание, что `floor` вместо Bin используется:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

В настоящее время `render` не имеет правильной метки длительности, но `| render columnchart` вместо этого можно использовать:

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

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

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

Выполните деление `1h` на, чтобы превратить ось x в число часов, а не на длительность:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

## <a name="join"></a>Join

Как найти две заданных Евенттипес в том, какое состояние произошло в обоих случаях?

Можно извлечь события с помощью первого EventType и второго EventType, а затем объединить два набора в состоянии.

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

:::image type="content" source="images/tutorial/join-events-la.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

## <a name="user-session-example-of-join"></a>Пример сеанса пользователя с присоединением

В этом разделе таблица не используется `StormEvents` .

Предположим, что имеются данные, включающие события, помечающие начало и конец каждого сеанса пользователя, с уникальным ИДЕНТИФИКАТОРом для каждого сеанса. 

Сколько времени последний пользовательский сеанс?

Используя `extend` для предоставления псевдонима для двух меток времени, можно вычислить длительность сеанса.

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

Рекомендуется использовать `project` только для выбора необходимых столбцов перед выполнением соединения.
В тех же предложениях мы переименуем столбец timestamp.

## <a name="plot-a-distribution"></a>Построение графика распределения

Сколько различий имеет разную длину?

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

Или используйте `| render columnchart` :

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

## <a name="percentiles"></a>Процентили

Какие диапазоны длительности охватывают различные процентные доли?

Используйте приведенный выше запрос, но замените `render` на:

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

В этом случае мы предоставили `by` предложение No, поэтому результатом будет одна строка:

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

Из полученных результатов мы видим следующее:

* 5% степени имеют длительность меньше 5 мин;
* 50% признаков за последние меньше 1 ч 25m;
* 5% последних по крайней мере 2H 50m.

Чтобы получить отдельную декомпозицию для каждого состояния, мы просто перенесем столбец State отдельно с помощью обоих операторов суммирования:

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Гистограмма счетчиков событий с различными состояниями&quot;:::

Хотя мы удалили `mid` операцию проекта, она все еще нужна, если нам нужно, чтобы на диаграмме отображались страны в таком порядке.

Строго говоря, &quot;Render":::

## <a name="let-assign-a-result-to-a-variable"></a>Оператор let: присвоение результата переменной

Используйте [let](./letstatement.md) , чтобы отделить части выражения запроса в примере "Join" выше. Результаты не изменились:

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
> В клиенте обозревателя Kusto не вставляйте пустые строки между частями этого объекта. Обязательно выполните все части.

## <a name="combining-data-from-several-databases-in-a-query"></a>Объединение данных из нескольких баз данных в запросе

Подробные сведения см. в статье [запросы между базами данных](./cross-cluster-or-database-queries.md)

При написании запроса стиля:

```kusto
Logs | where ...
```

Таблица с именем Logs должна находиться в базе данных по умолчанию. Если требуется доступ к таблицам из другой базы данных, используйте следующий синтаксис:

```kusto
database("db").Table
```

Поэтому при наличии баз данных с именем " *Диагностика* и *телеметрии* " и необходимости сопоставления некоторых их данных можно написать (предполагается, что система *диагностики* является базой данных по умолчанию).

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

или, если база данных по умолчанию является *телеметрии*

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Все указанные выше предполагают, что обе базы данных находятся в кластере, к которому в настоящее время установлено соединение. Предположим, что база данных *телеметрии* находится в другом кластере с именем *TelemetryCluster.kusto.Windows.NET* , а затем обращается к ней, вам потребуется

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Если кластер указан, база данных является обязательной.

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
