---
title: Примеры для Azure обозреватель данных
description: В этой статье описываются примеры в Azure обозреватель данных.
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
ms.openlocfilehash: b448f4249c777d9b9d61e58dad993f3da1817fda
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512474"
---
# <a name="samples"></a>Примеры

::: zone pivot="azuredataexplorer"

Ниже приведены некоторые распространенные потребности в запросах, а также способы использования языка запросов Kusto для их удовлетворения.

## <a name="display-a-column-chart"></a>Отображение гистограммы

Выполните проект с двумя или более столбцами и используйте их в качестве оси x и y диаграммы.

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* В первом столбце образуется ось x. Это может быть числовой, DateTime или String. 
* Используйте `where` , `summarize` и, `top` чтобы ограничить объем отображаемых данных.
* Отсортируйте результаты, чтобы определить порядок оси x.

:::image type="content" source="images/samples/060.png" alt-text="Снимок экрана гистограммы. Ось y лежит в диапазоне от 0 до примерно 50. В десяти цветных столбцах показаны соответствующие значения в 10 расположениях.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Получение сеансов от событий начала и окончания

Предположим, у вас есть журнал событий. Некоторые события отмечают начало или конец расширенного действия или сеанса. 

|Имя|Город|SessionId|Отметка времени|
|---|---|---|---|
|Запуск|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Запуск|Манчестер|4267667|2015-12-09T10:14:02.23|
|Остановить|London|2817330|2015-12-09T10:23:43.18|
|Отмена|Манчестер|4267667|2015-12-09T10:27:26.29|
|Остановить|Манчестер|4267667|2015-12-09T10:28:31.72|

Каждое событие имеет идентификатор SessionId. Проблема состоит в том, чтобы сопоставить события запуска и завершения с одним и тем же ИДЕНТИФИКАТОРом.

```kusto
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on SessionId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

1. Используйте [`let`](./letstatement.md) для именования проекции таблицы, которая урезанные вниз до перехода в соединение.
1. Используйте [`Project`](./projectoperator.md) для изменения имен меток времени, чтобы в результате могли появляться и время запуска, и значение времени окончания. 
   Он также выбирает другие столбцы для просмотра в результате. 
1. Используйте [`join`](./joinoperator.md) для сопоставления записей начала и окончания для одного и того же действия, создавая строку для каждого действия. 
1. Наконец, `project` снова добавляет столбец для отображения длительности действия.


|City|SessionId|StartTime|StopTime|Длительность|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Манчестер|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>Получение сеансов без идентификатора сеанса

Предположим, что события запуска и завершения не имеют удобного идентификатора сеанса, который можно сопоставить с. Но у нас есть IP-адрес клиента, на котором выполнялся сеанс. Если каждый клиент проводит только один сеанс одновременно, то можно сопоставить каждое событие начала следующему событию окончания с того же IP-адреса.

```kusto
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize arg_min(duration, *) by bin(StartTime,1s), ClientIp
```

Соединение будет сопоставлять каждое событие начала всем событиям окончания с того же IP-адреса клиента. 
1. Удаление совпадений с более ранним временем окончания.
1. Сгруппируйте по времени начала и IP, чтобы получить группу для каждого сеанса. 
1. Укажите `bin` функцию для параметра StartTime. В противном случае Kusto будет автоматически использовать 1-часовой интервал, который будет соответствовать времени начала с неверным временем окончания.

`arg_min` выбирает строку с наименьшей длительностью в каждой группе, а `*` параметр проходит через все остальные столбцы. Префикс аргумента "min_" для каждого имени столбца. 

:::image type="content" source="images/samples/040.png" alt-text="Таблица, в которой перечислены результаты, столбцы для времени начала, клиента P, длительности, города и самой ранней остановкой для каждой комбинации времени запуска клиента."::: 

Добавьте код для подсчета длительностей ячеек удобного размера. В этом примере из-за настройки линейчатой диаграммы разделите на, `1s` чтобы преобразовать интервалы времени в числа. 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="Гистограмма, на которой показано количество сеансов с длительностью в указанных диапазонах. Свыше 400 сеансов последней 10 секунд. Менее 100 — 290 секунд.":::

### <a name="real-example"></a>Реальный пример

```kusto
Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"      
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

## <a name="chart-concurrent-sessions-over-time"></a>Диаграммы для параллельных сеансов

Предположим, что у вас есть таблица действий с временем начала и окончания. Отображение диаграммы с течением времени, показывающей, сколько действий параллельно выполняется в любое время.

Ниже приведен пример входных данных с именем `X` .

|SessionId | StartTime | StopTime |
|---|---|---|
| а | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| с | 10:03:02 | 10:05:20 |

Для диаграммы с 1-минутными ячейками создайте что-то, которое в каждом интервале 1 МБ имеется число для каждого выполняемого действия.

Ниже приведен промежуточный результат.

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` создает массив значений через заданные интервалы.

|SessionId | StartTime | StopTime  | примеры|
|---|---|---|---|
| а | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| с | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Вместо того чтобы хранить эти массивы, разверните их, используя [MV-Expand](./mvexpandoperator.md).

```kusto
X | mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
```

|SessionId | StartTime | StopTime  | примеры|
|---|---|---|---|
| а | 10:01:33 | 10:06:31 | 10:01:00|
| а | 10:01:33 | 10:06:31 | 10:02:00|
| а | 10:01:33 | 10:06:31 | 10:03:00|
| а | 10:01:33 | 10:06:31 | 10:04:00|
| а | 10:01:33 | 10:06:31 | 10:05:00|
| а | 10:01:33 | 10:06:31 | 10:06:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| с | 10:03:12 | 10:04:30 | 10:03:00|
| с | 10:03:12 | 10:04:30 | 10:04:00|

Теперь сгруппируйте их по образцу времени, подчисляя вхождения каждого действия.

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Используйте ToDateTime (), так как [MV-Expand](./mvexpandoperator.md) возвращает столбец динамического типа.
* Используйте ячейку (), поскольку для числовых значений и дат функция суммирования всегда применяет функцию bin с интервалом по умолчанию, если вы не предоставляете его. 


| count_SessionId | примеры|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Результаты можно визуализировать в виде линейчатой диаграммы или диаграммы времени.

## <a name="introduce-null-bins-into-summarize"></a>Ввод в итоги пустых ячеек

Когда `summarize` оператор применяется к ключу группы, состоящему из `datetime` столбца, "bin" эти значения заносятся в ячейки фиксированной ширины.

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

В приведенном выше примере создается таблица с одной строкой для каждой группы строк `T` , попадающие в каждую ячейку за пять минут. В этом случае добавляются "пустые ячейки" — строки для значений времени ячеек между `StartTime` и `StopTime` для которых нет соответствующей строки в `T` . 

Желательно «раскладка» таблицу с этими ячейками. Вот один из способов сделать это.

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| summarize Count=count() by bin(Timestamp, 5m)
| where ...
| union ( // 1
  range x from 1 to 1 step 1 // 2
  | mv-expand Timestamp=range(StartTime, StopTime, 5m) to typeof(datetime) // 3
  | extend Count=0 // 4
  )
| summarize Count=sum(Count) by bin(Timestamp, 5m) // 5 
```

Ниже приведено пошаговое описание приведенного выше запроса. 

1. `union`Оператор позволяет добавлять в таблицу дополнительные строки. Эти строки создаются `union` выражением.
1. `range`Оператор создает таблицу с одной строкой или столбцом.
   Таблица не используется ни для чего не для `mv-expand` работы.
1. `mv-expand`Оператор над `range` функцией создает столько строк, сколько в 5-минутных ячейках между `StartTime` и `EndTime` .
1. Используйте `Count` `0` .
1. `summarize`Оператор группирует ячейки из исходного (левого или внешнего) аргумента в `union` . Оператор также является ячейкой из внутреннего аргумента к нему (строки пустых ячеек). Этот процесс обеспечивает наличие в выходных данных одной строки для каждой ячейки, значение которой равно нулю или является исходным числом.  

## <a name="get-more-out-of-your-data-in-kusto-with-machine-learning"></a>Получите больше данных в Kusto с помощью Машинное обучение 

Существует много интересных вариантов использования, которые используют алгоритмы машинного обучения и получают интересные сведения из данных телеметрии. Часто для этих алгоритмов в качестве входных данных требуется очень структурированный DataSet. Необработанные данные журнала, как правило, не соответствуют требуемой структуре и размеру. 

Начните с поиска аномалий в частоте ошибок определенной службы вывода Bing. Таблица LOGS содержит записи 65B. Простой запрос ниже фильтрует ошибки 250 000 и создает данные временных рядов, которые являются счетчиками ошибок, в которых используется функция обнаружения аномалий [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Аномалии обнаруживаются службой Kusto и выделяются красными точками на диаграмме временных рядов.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Служба определила несколько временных контейнеров с подозрительными частотами ошибок. Используйте Kusto, чтобы увеличить этот промежуток времени, и выполните запрос, который выполняет статистическую обработку в столбце Message. Попробуйте найти самые популярные ошибки. 

Соответствующие части всей трассировки стека сообщения обрезаются для лучшего размещения на странице. 

Можно увидеть успешную идентификацию первых восьми ошибок. Однако в этом случае возникает большая последовательность ошибок, так как сообщение об ошибке было создано строкой формата, содержащей изменяемые данные. 

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|Сообщение
|---|---
|7125|Сбой Ексекутеалгорисммесод для метода "Рунциклефроминтеримдата"...
|7125|Инференцехостсервице вызов failed..SysTEM. NullReferenceException: ссылка на объект не задает экземпляр объекта...
|7124|Непредвиденная система вывода error..SysTEM. NullReferenceException: ссылка на объект не задает экземпляр объекта... 
|5112|Непредвиденная система вывода error..SysTEM. NullReferenceException: ссылка на объект не задает экземпляр объекта..
|174|Инференцехостсервице Call failed..SysTEM. ServiceModel. CommunicationException: произошла ошибка при записи в канал:...
|10|Сбой Ексекутеалгорисммесод для метода "Рунциклефроминтеримдата"...
|10|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. Service. Managers. Усеринтеримдатаманажерексцептион:...
|3|Инференцехостсервице вызов failed..SysTEM. ServiceModel. Коммуникатионобжектфаултедексцептион:...
|1|Ошибка системы вывода... СоЦиалграф. начальник. Оператионреспонсе... AIS TraceId: 8292FC561AC64BED8FA243808FE74EFD...
|1|Ошибка системы вывода... СоЦиалграф. начальник. Оператионреспонсе... AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF...

Именно здесь `reduce` оператор помогает. Оператор определил 63 различных ошибок, которые были вызваны одной и той же точкой инструментирования трассировки в коде, и помогает сосредоточиться на дополнительных значимых трассировках ошибок в этом временном окне.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Счетчик|Модель
|---|---
|7125|Сбой Ексекутеалгорисммесод для метода "Рунциклефроминтеримдата"...
|  7125|Инференцехостсервице вызов failed..SysTEM. NullReferenceException: ссылка на объект не задает экземпляр объекта...
|  7124|Непредвиденная система вывода error..SysTEM. NullReferenceException: ссылка на объект не задает экземпляр объекта...
|  5112|Непредвиденная система вывода error..SysTEM. NullReferenceException: ссылка на объект не задает экземпляр объекта..
|  174|Инференцехостсервице Call failed..SysTEM. ServiceModel. CommunicationException: произошла ошибка при записи в канал:...
|  63|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. \* : Write \* для записи в объект начальника. \* : соЦиалграф. начальник. запросы...
|  10|Сбой Ексекутеалгорисммесод для метода "Рунциклефроминтеримдата"...
|  10|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. Service. Managers. Усеринтеримдатаманажерексцептион:...
|  3|Инференцехостсервице вызов failed..SysTEM. ServiceModel. \* : объект, System. ServiceModel. Channels. \* + \* , для \* \* — это \* ...   в пере...

Теперь у вас есть хорошее представление об основных ошибках, которые привели к обнаруженным аномалиям.

Чтобы понять влияние этих ошибок на систему образца, выполните следующие действия. 
* Таблица LOGS содержит дополнительные многомерные данные, такие как "Component", "Cluster" и т. д.
* Новый подключаемый модуль "автокластер" может помочь получить представление о простом запросе. 
* В приведенном ниже примере можно ясно увидеть, что каждая из четырех основных ошибок относится только к компоненту. Кроме того, хотя три основных ошибки относятся к кластеру DB4, четвертый из них происходит во всех кластерах.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Счетчик |Процент (%)|Компонент|Кластер|Сообщение
|---|---|---|---|---
|7125|26,64|инференцехостсервице|DB4|Ексекутеалгорисммесод для метода....
|7125|26,64|Неизвестный компонент|DB4|Сбой вызова Инференцехостсервице...
|7124|26,64|инференцеалгорисмексекутор|DB4|Непредвиденная системная ошибка вывода...
|5112|19,11|инференцеалгорисмексекутор|*|Непредвиденная системная ошибка вывода... 

## <a name="map-values-from-one-set-to-another"></a>Сопоставьте значения из одного набора с другим

Распространенным вариантом использования является статическое сопоставление значений, что может помочь сделать результаты более выступающими.
Например, рассмотрим следующую таблицу. 
`DeviceModel` Указывает модель устройства, которая не является очень удобной формой ссылки на имя устройства.  


|DeviceModel |Счетчик 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

  
Ниже представлено лучшее представление.  

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

В двух приведенных ниже подходах показано, как можно достичь представления.  

### <a name="mapping-using-dynamic-dictionary"></a>Сопоставление с использованием динамического словаря

Этот подход показывает сопоставление с динамическим словарем и динамическими методами доступа.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Data set definition
let Source = datatable(DeviceModel:string, Count:long)
[
  'iPhone5,1', 32,
  'iPhone3,2', 432,
  'iPhone7,2', 55,
  'iPhone5,2', 66,
];
// Query start here
let phone_mapping = dynamic(
  {
    "iPhone5,1" : "iPhone 5",
    "iPhone3,2" : "iPhone 4",
    "iPhone7,2" : "iPhone 6",
    "iPhone5,2" : "iPhone5"
  });
Source 
| project FriendlyName = phone_mapping[DeviceModel], Count
```

|FriendlyName|Счетчик|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|

### <a name="map-using-static-table"></a>Схема с использованием статической таблицы

В этом подходе показано сопоставление с постоянной таблицей и оператором join.
 
Создайте таблицу сопоставления только один раз.

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Теперь содержимое устройств.

|DeviceModel |FriendlyName 
|---|---
|iPhone5, 1 |iPhone 5 
|iPhone3, 2 |iPhone 4 
|iPhone7, 2 |iPhone 6 
|iPhone5, 2 |iPhone5 

Используйте тот же прием для создания источника тестовой таблицы.

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```

Присоединение и проект.

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

Результат:

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Создание и использование таблиц измерений времени выполнения запросов

Часто возникает необходимость объединить результаты запроса с некоторой таблицей нерегламентированного измерения, которая не хранится в базе данных. Можно определить выражение, результатом которого является таблица, ограниченная одним запросом. Пример:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Create a query-time dimension table using datatable
let DimTable = datatable(EventType:string, Code:string)
  [
    "Heavy Rain", "HR",
    "Tornado",    "T"
  ]
;
DimTable
| join StormEvents on EventType
| summarize count() by Code
```

Вот чуть более сложный пример.

```kusto
// Create a query-time dimension table using datatable
let TeamFoundationJobResult = datatable(Result:int, ResultString:string)
  [
    -1, 'None', 0, 'Succeeded', 1, 'PartiallySucceeded', 2, 'Failed',
    3, 'Stopped', 4, 'Killed', 5, 'Blocked', 6, 'ExtensionNotFound',
    7, 'Inactive', 8, 'Disabled', 9, 'JobInitializationError'
  ]
;
JobHistory
  | where PreciseTimeStamp > ago(1h)
  | where Service  != "AX"
  | where Plugin has "Analytics"
  | sort by PreciseTimeStamp desc
  | join kind=leftouter TeamFoundationJobResult on Result
  | extend ExecutionTimeSpan = totimespan(ExecutionTime)
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage
```

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>Получение последних записей (по метке времени) на удостоверение

Предположим, что у вас есть таблица, которая включает в себя следующее:
* `ID`столбец, определяющий сущность, с которой связана каждая строка, например идентификатор пользователя или идентификатор узла.
* `timestamp`столбец, предоставляющий ссылку времени для строки.
* другие столбцы

Запрос, возвращающий последние две записи для каждого значения `ID` столбца, где "latest" определяется как "с наибольшим значением `timestamp` ", можно с помощью [верхнего вложенного оператора](topnestedoperator.md).

Пример:

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // #1
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // #2
  top-nested 2 of timestamp by dummy1=max(timestamp),          // #3
  top-nested   of bla       by dummy2=max(1)                   // #4
| project-away dummy0, dummy1, dummy2                          // #5
```

Нумерация заметок ниже относится к числу в образце кода, который находится далеко справа.

1. `datatable`— Это способ создания некоторых тестовых данных для демонстрационных целей. Обычно здесь вы используете реальные данные.
1. Эта строка по сути означает «возврат всех различных значений `id` ».
1. Затем эта строка возвращает для двух первых записей, которые максимально подвышают:
     * `timestamp`столбец
     * столбцы предыдущего уровня (здесь просто `id` )
     * столбец, указанный на этом уровне (здесь, `timestamp` )
1. Эта строка добавляет значения `bla` столбца для каждой записи, возвращенной предыдущим уровнем. Если таблица содержит другие интересующие столбцы, можно повторить эту строку для каждого такого столбца.
1. В этой конечной строке для удаления "лишних" столбцов, появившихся, используется [оператор размещения по проекту](projectawayoperator.md) `top-nested` .

## <a name="extend-a-table-with-some-percent-of-total-calculation"></a>Расширение таблицы при расчете процента от общего числа

Табличное выражение, включающее числовой столбец, более удобно для пользователя, когда он сопровождается его значением в процентах от общего числа. Например, предположим, что существует запрос, создающий следующую таблицу:

|сомесериес|сомеинт|
|----------|-------|
|Foo       |    100|
|Гистограмма       |    200|

Если вы хотите отобразить эту таблицу как:

|сомесериес|сомеинт|Освобождаемый |
|----------|-------|----|
|Foo       |    100|33,3|
|Гистограмма       |    200|66,6|

Затем необходимо вычислить сумму (SUM) `SomeInt` столбца, а затем разделить каждое значение этого столбца на общее. Для произвольных результатов используйте [оператор as](asoperator.md).

```kusto
// The following table literal represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Foo",
  200, "Bar",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>Выполнение агрегатов в скользящем окне

В следующем примере показано, как суммировать столбцы с помощью скользящего окна.
Используйте таблицу ниже, которая содержит цены на фруктам по меткам времени. Вычислите минимальные, максимальные и суммарные затраты на каждый фруктов в день, используя скользящее окно из семи дней. Каждая запись в результирующем наборе объединяет предыдущие семь дней, а результат содержит запись в день анализа.  

Таблица фруктам:

|Отметка времени|Фрукт|Цена|
|---|---|---|
|2018-09-24 21:00:00.0000000|Бананы|3|
|2018-09-25 20:00:00.0000000|Яблоки|9|
|2018-09-26 03:00:00.0000000|Бананы|4|
|2018-09-27 10:00:00.0000000|Подушку|8|
|2018-09-28 07:00:00.0000000|Бананы|6|
|2018-09-29 21:00:00.0000000|Бананы|8|
|2018-09-30 01:00:00.0000000|Подушку|2|
|2018-10-01 05:00:00.0000000|Бананы|0|
|2018-10-02 02:00:00.0000000|Бананы|0|
|2018-10-03 13:00:00.0000000|Подушку|4|
|2018-10-04 14:00:00.0000000|Яблоки|8|
|2018-10-05 05:00:00.0000000|Бананы|2|
|2018-10-06 08:00:00.0000000|Подушку|8|
|2018-10-07 12:00:00.0000000|Бананы|0|

Запрос агрегирования скользящего окна.
Пояснение следует за результатами запроса:

```kusto
let _start = datetime(2018-09-24);
let _end = _start + 13d; 
Fruits 
| extend _bin = bin_at(Timestamp, 1d, _start) // #1 
| extend _endRange = iif(_bin + 7d > _end, _end, 
                            iff( _bin + 7d - 1d < _start, _start,
                                iff( _bin + 7d - 1d < _bin, _bin,  _bin + 7d - 1d)))  // #2
| extend _range = range(_bin, _endRange, 1d) // #3
| mv-expand _range to typeof(datetime) limit 1000000 // #4
| summarize min(Price), max(Price), sum(Price) by Timestamp=bin_at(_range, 1d, _start) ,  Fruit // #5
| where Timestamp >= _start + 7d; // #6

```

|Отметка времени|Фрукт|min_Price|max_Price|sum_Price|
|---|---|---|---|---|
|2018-10-01 00:00:00.0000000|Яблоки|9|9|9|
|2018-10-01 00:00:00.0000000|Бананы|0|8|18|
|2018-10-01 00:00:00.0000000|Подушку|2|8|10|
|2018-10-02 00:00:00.0000000|Бананы|0|8|18|
|2018-10-02 00:00:00.0000000|Подушку|2|8|10|
|2018-10-03 00:00:00.0000000|Подушку|2|8|14|
|2018-10-03 00:00:00.0000000|Бананы|0|8|14|
|2018-10-04 00:00:00.0000000|Бананы|0|8|14|
|2018-10-04 00:00:00.0000000|Подушку|2|4|6|
|2018-10-04 00:00:00.0000000|Яблоки|8|8|8|
|2018-10-05 00:00:00.0000000|Бананы|0|8|10|
|2018-10-05 00:00:00.0000000|Подушку|2|4|6|
|2018-10-05 00:00:00.0000000|Яблоки|8|8|8|
|2018-10-06 00:00:00.0000000|Подушку|2|8|14|
|2018-10-06 00:00:00.0000000|Бананы|0|2|2|
|2018-10-06 00:00:00.0000000|Яблоки|8|8|8|
|2018-10-07 00:00:00.0000000|Бананы|0|2|2|
|2018-10-07 00:00:00.0000000|Подушку|4|8|12|
|2018-10-07 00:00:00.0000000|Яблоки|8|8|8|

Сведения о запросе:

Запрос растягивает (дублирует) каждую запись во входной таблице в течение семи дней после ее фактического отображения. Каждая запись на самом деле отображается семь раз.
В результате ежедневное агрегирование включает все записи за предыдущие семь дней.

Пошаговое описание приведенных ниже цифр относится к числу в образце кода, которое находится далеко справа:
1. Регистрировать каждую запись в один день (относительно _start). 
2. Определите конец диапазона на запись (_bin + 7D), если это не выходит за пределы диапазона _(начальный, конечный)_ , в этом случае он корректируется. 
3. Для каждой записи создайте массив из семи дней (меток времени), начиная с текущего дня записи. 
4. MV — расширение массива, таким путем дублирование каждой записи в семь записей, один день друг за другом. 
5. Выполняйте статистическую функцию для каждого дня. Из-за #4 на самом деле суммируется _за последние_ семь дней. 
6. Данные за первые семь дней не являются полными. В течение первых семи дней нет 7D лукбакк периода. Первые семь дней исключаются из итогового результата. В этом примере они участвуют только в статистической обработке 2018-10-01.

## <a name="find-preceding-event"></a>Найти предыдущее событие
В следующем примере показано, как найти предыдущее событие между двумя наборами данных.  

*Назначение.*: существует два набора данных: A и B. Для каждой записи в B найдите предыдущее событие в (то есть запись arg_max в, которая по-прежнему имеет значение "более старый", чем B). Ниже приведен ожидаемый результат для следующих наборов данных-образцов. 

```kusto
let A = datatable(Timestamp:datetime, ID:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, ID:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Отметка времени|ID|евентб|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|з|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|да|Ay1|
|2019-01-01 00:00:05.0000000|да|Ay2|

</br>

|Отметка времени|ID|Событие а|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|да|B|
|2019-01-01 00:02:00.0000000|з|B|

Ожидаемые выходные данные: 

|ID|Отметка времени|евентб|A_Timestamp|Событие а|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|да|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|з|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Для этой проблемы предлагается два разных подхода. Чтобы найти наиболее подходящий для вас набор данных, необходимо протестировать оба этих параметра.

> [!NOTE] 
> Каждый метод может выполняться по-разному в разных наборах данных.

### <a name="suggestion-1"></a>#1 предложения

В этом предложении оба набора данных сериализуются по ИДЕНТИФИКАТОРу и метке времени, затем группируют все события B со всеми предшествующими событиями и выбирают из `arg_max` всех элементов, как в группе.

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by ID, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != ID), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, ID
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>#2 предложения

Для этого предложения требуется значение Max-лукбакк-period (то есть "старая" запись в может быть, по сравнению с B). Затем метод соединяет два набора данных по ИДЕНТИФИКАТОРу и этому лукбакк периоду. Соединение создает все возможные кандидаты, все записи, которые старше B и в течение лукбакк периода, а затем ближайший от 1 до B фильтр фильтруется по arg_min (Тиместампб — метка времени). Чем короче период лукбакк, тем лучше результаты запроса.

В приведенном ниже примере период лукбакк равен 1 МБ, а запись с ИДЕНТИФИКАТОРом "z" не имеет соответствующего события "A", так как его "A" старше 2 МБ.

```kusto 
let _maxLookbackPeriod = 1m;  
let _internalWindowBin = _maxLookbackPeriod / 2;
let B_events = B 
    | extend ID = new_guid()
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend B_Timestamp = Timestamp, _range;
let A_events = A 
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend A_Timestamp = Timestamp, _range;
B_events
    | join kind=leftouter (
        A_events
) on ID, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project ID, B_Timestamp, A_Timestamp, EventB, EventA
```

|Идентификатор|B_Timestamp|A_Timestamp|евентб|Событие а|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|да|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|з|2019-01-01 00:02:00.0000000||B||

::: zone-end

::: zone pivot="azuremonitor"

## <a name="string-operations"></a>Операции со строками
В следующих разделах приведены примеры работы со строками на языке запросов Kusto.

### <a name="strings-and-escaping-them"></a>Строки и их экранирование
Строковые значения заключаются в одинарные или двойные кавычки. Обратная косая черта ( \\ ) используется для экранирования символов до символа, следующего за ним, например \t для Tab, \n для новой строки и \" сам символ кавычки.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Чтобы символ \\ не интерпретировался как escape-символ, добавьте \@ в качестве префикса к строке:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>Сравнения строк

Оператор       |Описание                         |С учетом регистра|Пример (при `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Равно                              |Да           |`"aBc" == "aBc"`
`!=`           |Не равно                          |Да           |`"abc" != "ABC"`
`=~`           |Равно                              |Нет            |`"abc" =~ "ABC"`
`!~`           |Не равно                          |Нет            |`"aBc" !~ "xyz"`
`has`          |Правая часть представляет собой все слово в левой части |Нет|`"North America" has "america"`
`!has`         |Правая часть не является всем словом в левой части       |Нет            |`"North America" !has "amer"` 
`has_cs`       |Правая часть представляет собой все слово в левой части |Да|`"North America" has_cs "America"`
`!has_cs`      |Правая часть не является всем словом в левой части       |Да            |`"North America" !has_cs "amer"` 
`hasprefix`    |Правая часть представляет собой префикс слова в левой части         |Нет            |`"North America" hasprefix "ame"`
`!hasprefix`   |Правая часть не является префиксом слова в левой части     |Нет            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Правая часть представляет собой префикс слова в левой части         |Да            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Правая часть не является префиксом слова в левой части     |Да            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Правая часть представляет собой суффикс слова в левой части         |Нет            |`"North America" hassuffix "ica"`
`!hassuffix`   |Правая часть не является суффиксом слова в левой части     |Нет            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Правая часть представляет собой суффикс слова в левой части         |Да            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Правая часть не является суффиксом слова в левой части     |Да            |`"North America" !hassuffix_cs "icA"`
`contains`     |Правая часть является вхождением в левую часть  |Нет            |`"FabriKam" contains "BRik"`
`!contains`    |Правая часть не входит в левую часть           |Нет            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Правая часть является вхождением в левую часть  |Да           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Правая часть не входит в левую часть           |Да           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Правая часть является начальным вхождением в левую часть|Нет            |`"Fabrikam" startswith "fab"`
`!startswith`  |Правая часть не является начальным вхождением в левую часть|Нет        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Правая часть является начальным вхождением в левую часть|Да            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Правая часть не является начальным вхождением в левую часть|Да        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Правая часть является конечным вхождением в левую часть|Нет             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Правая часть не является конечным вхождением в левую часть|Нет         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Правая часть является конечным вхождением в левую часть|Да             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Правая часть не является конечным вхождением в левую часть|Да         |`"Fabrikam" !endswith "brik"`
`matches regex`|Левая часть содержит соответствие для правой части        |Да           |`"Fabrikam" matches regex "b.*k"`
`in`           |Соответствует одному из элементов       |Да           |`"abc" in ("123", "345", "abc")`
`!in`          |Не соответствует одному из элементов   |Да           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>countof

Подсчитывает вхождения подстроки в строку. Можно сопоставить текстовые строки или использовать регулярное выражение. Совпадения для текстовых строк могут перекрывать друг друга, при этом для регулярных выражений — не могут.

```
countof(text, search [, kind])
```

- `text` — строка входных данных. 
- `search` — текстовая строка или регулярное выражение, которое проверяется на вхождение в text.
- `kind` - _Обычная задача_  |  _Regex_ (по умолчанию — нормальный).

Возвращает число совпадений строки поиска в контейнере. Совпадения для текстовых строк могут перекрывать друг друга, при этом для регулярных выражений — не могут.

#### <a name="plain-string-matches"></a>Совпадения текстовой строки

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Совпадения регулярного выражения

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>extract

Получает соответствие регулярного выражения из указанной строки. При необходимости также преобразует извлеченную подстроку в указанный тип.

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex` — регулярное выражение.
- `captureGroup` — положительная целочисленная константа, указывающая извлекаемую группу записи. 0 для полного совпадения, 1 — значение, соответствующее первой "("круглой скобке")" в регулярном выражении, 2 или более — последующим круглым скобкам.
- `text` — строка для поиска.
- `typeLiteral` — необязательный литерал типа (например, typeof(long)). Если указан, то извлеченная подстрока преобразуется в этот тип.

Возвращает подстроку, совпадающую с указанной группой захвата Каптуреграуп, при необходимости преобразованную в Типелитерал.
Если соответствия нет или не удается выполнить преобразование типа, возвращается NULL.


Следующий пример извлекает последний октет *ComputerIP* из записи пакета пульса:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Следующий пример извлекает последний октет, приводит его к типу (числа) *real* и вычисляет следующее значение IP-адреса:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Ниже в примере строки *Trace* производится поиск определения "Duration". Соответствие приводится к типу *real* и умножается на константу времени (1s), *которая приводит Duration к типу timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* возвращает значение true, если аргумент является пустой строкой или NULL (см. также *isnull*).
- *isnotempty* возвращает значение true, если аргумент не является пустой строкой или NULL (см. также *isnotnull*), Псевдоним: *notempty*.


```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Примеры

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>parseurl

Разделяет URL-адрес на части (протокол, узел, порт и т. д.) и возвращает объект словаря, содержащий элементы в виде строк.

```
parseurl(urlstring)
```

#### <a name="examples"></a>Примеры

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Результат будет таким:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


### <a name="replace"></a>replace

Заменяет все найденные совпадения регулярного выражения другой строкой. 

```
replace(regex, rewrite, input_text)
```

- `regex` — регулярное выражение для сравнения. Оно может содержать группы захвата в '('круглых скобках')'.
- `rewrite` — регулярное выражение, результат которого заменит все соответствия, найденные сопоставлениями регулярного выражения. Используйте \0 для указания полного соответствия, \1 для первой группы записи, \2 и так далее — для следующих групп записи.
- `input_text` — строка входных данных для поиска.

Возвращает текст после замены всех совпадений Regex с вычислениями перезаписи. Совпадения не перекрываются.


```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Можно получить следующие результаты:

Действие                                        |заменена
------------------------------------------------|----------------------------------------------------------
4663 — была предпринята попытка доступа к объекту.  |Идентификатор действия 4663 — была предпринята попытка доступа к объекту.


### <a name="split"></a>разделение

Разделяет указанную строку в соответствии с указанным разделителем и возвращает массив результирующих подстрок.

```
split(source, delimiter [, requestedIndex])
```

- `source` — строка для разделения в соответствии с указанным разделителем.
- `delimiter` — разделитель, который будет использоваться для разделения исходной строки.
- `requestedIndex` — необязательный индекс, начинающийся с нуля. Если указан, то возвращаемый массив строк будет содержать только этот элемент (если существует).


#### <a name="examples"></a>Примеры

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>strcat

Сцепляет строковые аргументы (поддерживает от 1 до 16 аргументов).

```
strcat("string1", "string2", "string3")
```

#### <a name="examples"></a>Примеры
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>strlen

Возвращает длину строки.

```
strlen("text_to_evaluate")
```

#### <a name="examples"></a>Примеры
```Kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>substring

Извлекает подстроку из заданной исходной строки, начиная с указанного индекса. При необходимости можно указать длину запрашиваемой подстроки.

```
substring(source, startingIndex [, length])
```

- `source` — исходная строка, из которой будет извлечена подстрока.
- `startingIndex` — отсчитываемая от нуля позиция первого знака запрашиваемой подстроки.
- `length` — необязательный параметр, который указывает требуемую длину возвращаемой подстроки.

#### <a name="examples"></a>Примеры
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>tolower, toupper

Преобразует все буквы заданной строки в нижний или верхний регистр.

```
tolower("value")
toupper("value")
```

#### <a name="examples"></a>Примеры
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>Операции с датой и временем
В следующих разделах приведены примеры работы со значениями даты и времени на языке запросов Kusto.

### <a name="date-time-basics"></a>Основные сведения о дате и времени
Язык запросов Kusto содержит два основных типа данных, связанных с датами и временем: datetime и timespan. Все даты задаются в формате UTC. Хотя поддерживаются несколько форматов даты и времени, рекомендуется использовать формат ISO8601. 

Интервалы времени выражаются как десятичное число, за которым следует единица времени:

|сокращение   | единица времени    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | миллисекунда  |
|микросекунда | микросекунда  |
|галочка        | наносекунда   |

Значения даты и времени можно создать путем преобразования строки с помощью оператора `todatetime`. Например, чтобы просмотреть пульс виртуальной машины, отправленный в определенный период времени, можно использовать оператор `between`, с помощью которого удобнее указывать диапазон времени.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Другим распространенным сценарием является сравнение даты и времени с настоящим временем. Например, чтобы просмотреть все пакеты пульса за последние две минуты, можно использовать оператор `now` вместе с промежутком времени в две минуты:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Для этой функции также доступно сокращение:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Кратчайший и самый удобочитаемый метод — использовать оператор `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Предположим, что вместо времени начала и окончания вы знаете время начала и длительность. Запрос можно переписать следующим образом:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="converting-time-units"></a>Преобразование единиц измерения времени
Понадобится выразить дату и время или интервал времени в единицах времени, отличных от значения по умолчанию. Предположим, вы просматриваете события ошибок за последние 30 минут и нуждаетесь в вычисляемом столбце, который показывает, как давно произошло событие.

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo`Столбец содержит такие значения: "00:09:31.5118992", то есть они форматируются как чч: мм: СС. fffffff. Если вы хотите форматировать эти значения в `numver` минут с времени начала, укажите timeAgo/1m.

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


### <a name="aggregations-and-bucketing-by-time-intervals"></a>Агрегирование и группирование по интервалам времени
Другим очень распространенным сценарием является необходимость получения статистики за определенный период времени и для определенного интервала. Для этого можно использовать оператор `bin` как часть предложения итоговых значений.

Чтобы получить количество событий, которые происходили каждые 5 минут в течение последнего получаса, используйте следующий запрос:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

В результате вы получите следующую таблицу:  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Другой способ создания контейнеров результатов — использовать функции, такие как `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Вы получите следующие результаты:

|TIMESTAMP|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>Часовые пояса
Поскольку все значения даты и времени выражены в формате UTC, возможно будет удобнее, если преобразовать эти значения в формат локального времени. Например, используйте это вычисление для преобразования времени UTC в формат PST (тихоокеанское время):

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Агрегации
В следующих разделах приведены примеры статистической обработки результатов запроса на языке запросов Kusto.

### <a name="count"></a>count
Выполните подсчет количества строк в результирующем наборе после применения фильтров. Следующий пример возвращает общее количество строк в таблице _Perf_ за последние 30 минут. Результат возвращается в столбец под именем *count_*, если присвоено конкретное имя:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Чтобы просмотреть тенденцию по времени, можно использовать визуализацию временной диаграммы:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

В результате этого примера показана линия тенденции количества записей perf с интервалами в 5 минут:

![Тенденция подсчета](images/samples/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Используйте `dcount` и `dcountif`, чтобы подсчитать уникальные значения в определенном столбце. Следующий запрос вычисляет, сколько отдельных компьютеров отправили пакеты пульса за последний час:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Чтобы подсчитать только компьютеры Linux, отправлявшие пакеты пульса, используйте `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Оценка подгрупп
Чтобы выполнить подсчет или другие агрегирования с подгруппами в данных, используйте ключевое слово `by`. Например, чтобы подсчитать количество уникальных компьютеров Linux, которые отправляли пульсы в каждой стране или регионе, сделайте следующее:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|США    | 19                  |
|Канада           | 3                   |
|Ирландия          | 0                   |
|Соединенное Королевство   | 0                   |
|Нидерланды      | 2                   |


Чтобы проанализировать меньшие подгруппы данных, добавьте имена дополнительных столбцов в раздел `by`. Например, может потребоваться подсчитать различные компьютеры из каждой страны или региона на OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>Процентиль
Чтобы найти медиану, используйте функцию `percentile` со значением, чтобы указать процентиль:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Вы также можете указать различные процентили, чтобы получить агрегированный результат для каждого из них:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Таким образом можно определить, что некоторые ЦП имеют похожие медианы, но различные условия: в некоторых показатели стабильны возле медианы, а в других определяются более низкие и высокие значения ЦП. Это означает, что ЦП испытал пиковые нагрузки.

### <a name="variance"></a>Variance
Чтобы напрямую рассчитать дисперсию значения, используйте стандартное отклонение и методы дисперсии:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Хороший способ проанализировать стабильность загрузки ЦП — объединить значение stdev с вычислением медианы:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generating-lists-and-sets"></a>Создание списков и наборов
Вы можете использовать `makelist`, чтобы свести данные по порядку значений в отдельном столбце. Например, вы можете изучить наиболее распространенные события на компьютерах по порядку. Вы можете сводить данные по порядку идентификаторов событий на каждом компьютере. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Компьютер|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` формирует список в порядке, в котором были переданы данные. Чтобы отсортировать события от старых к новым, используйте `asc` в операторе порядка, а не `desc`. 

Также эффективно создавать список только уникальных значений. Такой список называется _набором_, и его можно создать с помощью `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Компьютер|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Как и `makelist`, `makeset` также работает с упорядоченными данными и генерирует массивы на основе порядка переданных строк.

### <a name="expanding-lists"></a>Развертывание списков
Обратной операцией `makelist` или `makeset` является инструкция `mvexpand`, которая расширяет список значений для разделения строк. Ее можно развернуть в любом количестве динамических столбцов — в формате JSON и массива. Например, вы можете просканировать таблицу *пульса* для определения решений, отправляющих данные с компьютеров, которые отправили пакет пульса за последний час:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Компьютер | Решения | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

Используйте `mvexpand`, чтобы показать каждое значение в отдельной строке вместо списка, разделенного запятыми:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Компьютер | Решения | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


Затем вы можете использовать `makelist` снова, чтобы сгруппировать элементы. Теперь вы можете просмотреть список компьютеров для каждого решения:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Решения | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="handling-missing-bins"></a>Обработка отсутствующих ячеек
Полезное приложение `mvexpand` — это необходимость заполнения значений по умолчанию в для отсутствующих ячеек. Например, предположим, что вы ищете время бесперебойной работы определенной машины, изучая его пульс. Также вы хотите просмотреть источник пульса, который находится в столбце _категории_. Как правило, мы используем простой оператор суммирования следующим образом:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Категория | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Однако, в этих результатах контейнер, связанный с "2017-06-06T19:00:00Z", отсутствует, так как отсутствуют данные пульса для этого времени. Используйте функцию `make-series`, чтобы присвоить значение по умолчанию пустым контейнерам. Это приведет к созданию строки для каждой категории с двумя дополнительными столбцами массива, одним для значений и одним для сопоставления контейнеров по времени:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Категория | count_ | TimeGenerated |
|---|---|---|
| Direct Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Третий элемент массива *count_*, как и ожидалось, равен 0, и есть соответствующая метка времени "2017-06-06T19:00:00.0000000Z" в массиве _TimeGenerated_. Однако этот формат массива трудно прочитать. Используйте `mvexpand`, чтобы развернуть массивы и создать выходные данные того же формата, что и сгенерированные `summarize`:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Категория | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T19:00:00Z | 0 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Сужение результатов до набора элементов: `let`, `makeset`, `toscalar`, `in`
Распространенным сценарием является выбор имен некоторых конкретных сущностей на основе набора критериев, а затем фильтрация другого набора данных до этого набора сущностей. Например, вы можете найти компьютеры, у которых отсутствуют обновления, и определить IP-адреса, для которых вызываются эти компьютеры:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>Соединения
Объединения позволяют анализировать данные из нескольких таблиц в одном запросе. Они объединяют строки двух наборов данных, сопоставляя значения из указанных столбцов.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

В этом примере первый набор данных фильтрует все действия входа. Он объединяется со вторым набором данных, который фильтрует действия выхода. Используемые столбцы: _Computer_, _Account_, _TargetLogonId_ и _TimeGenerated_. Наборы данных сопоставляются по общему столбцу _TargetLogonId_. Результат — одна запись на сопоставление, в которой есть и время входа и время выхода.

Если оба набора данных имеют столбцы с одинаковыми именами, столбцам набора данных справа присваивается номер индекса. Этот пример отобразит столбец _TargetLogonId_ со значениями в левой таблице и столбец _TargetLogonId1_ со значениями в правой. В этом случае второй столбец _TargetLogonId1_ был удален с помощью оператора `project-away`.

> [!NOTE]
> Для повышения производительности оставьте только соответствующие столбцы объединенных наборов данных, используя оператор `project`.


Используйте следующий синтаксис, чтобы объединить два набора данных и чтобы присоединенный ключ имел разные имена между двумя таблицами:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Таблицы подстановок
Обычно для объединения применяется статическое сопоставление значений с использованием `datatable`, что может помочь в преобразовании результатов в удобный вид. Например, для добавления к данным событий безопасности имени события для каждого идентификатора.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

| eventName | count_ |
|:---|:---|
| Маркер объекта был закрыт | 290 995 |
| Запрошен маркер объекта | 154 157 |
| Предпринята попытка скопировать маркер в объект | 144 305 |
| Предпринята попытка доступа к объекту | 123 669 |
| Криптографическая операция | 153 495 |
| Операция с файлом ключа | 153 496 |

## <a name="json-and-data-structures"></a>JSON и структуры данных

Вложенные объекты — это объекты, которые содержат другие объекты в виде массива или карты пар "ключ — значение". Эти объекты представлены как строки JSON. В этом разделе описывается использование JSON для извлечения данных и анализа вложенных объектов.

### <a name="working-with-json-strings"></a>Работа со строками JSON
Используйте `extractjson`, чтобы получить доступ к определенному элементу JSON с известным путем. Эта функция требует выражение пути, которое использует следующие правила.

- _$_ Ссылка на корневую папку
- Использование скобок или точечной нотации для обращения к индексам и элементам, как показано в следующих примерах.


Использование скобок для индексов и точек для разделения элементов:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Тот же пример с использованием только скобочной нотации:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Если имеется только один элемент, можно использовать только точечную нотацию:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>parsejson
Чтобы получить доступ к нескольким элементам в структуре json, проще открыть его как динамический объект. Используйте `parsejson` для приведения текстовых данных к динамическому объекту. После преобразования в динамический тип для анализа данных могут использоваться дополнительные функции.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Используйте `arraylength` для подсчета числа элементов в массиве:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Используйте `mvexpand`, чтобы разбить свойства объекта на отдельные строки.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![Снимок экрана показывает hosts_0 со значениями расположения, состояния и скорости.](images/samples/mvexpand.png)

### <a name="buildschema"></a>buildschema
Используйте `buildschema` для получения схемы, которая учитывает все значения объекта:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Выходные данные — это схема в формате JSON.
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Эти выходные данные описывают имена полей объекта и соответствующие им типы данных. 

Вложенные объекты могут иметь разные схемы, такие как в следующем примере:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Диаграммы
Следующие разделы содержат примеры работы с диаграммами на языке запросов Kusto.

### <a name="chart-the-results"></a>Диаграмма результатов
Начните с изучения того, сколько компьютеров работают в операционной системе за последний час.

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

По умолчанию результаты отображаются в виде таблицы:

![Таблица](images/samples/table-display.png)

Чтобы получить более полное представление, выберите **Диаграмма** и выберите параметр **Круговая**, чтобы отображать результаты:

![Круговая диаграмма](images/samples/charts-and-diagrams-pie.png)


### <a name="timecharts"></a>Временные диаграммы
Показать среднее количество 50- и 95-процентильной загруженности процессора в ячейках за 1 час. Запрос создает несколько рядов, а затем можно выбрать, какие ряды отображать на диаграмме времени.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Выберите параметр **Показать график:**

![График](images/samples/charts-and-diagrams-multiple-series.png)

#### <a name="reference-line"></a>Справочные данные

Справочные данные помогут легко определить, превышает ли метрика указанный порог. Чтобы добавить строку в диаграмму, расширьте набор данных столбцом константы.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Справочные данные](images/samples/charts-and-diagrams-multiple-series-threshold.png)

### <a name="multiple-dimensions"></a>Несколько измерений
Несколько выражений в значении `by` из `summarize` в результате создают несколько строк, по одному для каждой комбинации значений.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

При просмотре результатов в виде диаграммы используется первый столбец из предложения `by`. В следующем примере показана гистограмма с накоплением с использованием _EventID._ Измерения должны быть типа `string`, поэтому в этом примере _EventID_ приводится к строкам. 

![Гистограмма EventID](images/samples/charts-and-diagrams-multiple-dimension-1.png)

Можно переключаться между ними, выбирая выпадающее меню с именем столбца. 

![Гистограмма AccountType](images/samples/charts-and-diagrams-multiple-dimension-2.png)

## <a name="smart-analytics"></a>Смарт-аналитика
Этот раздел содержит примеры использования функций интеллектуального анализа в Log Analytics для выполнения анализа действий пользователей. Вы можете использовать эти примеры для анализа приложений, отслеживаемых Application Insights, или применять общие сведения из этих запросов для выполнения аналогичного анализа других данных. 

### <a name="cohorts-analytics"></a>Аналитика когорт

Анализ когорт позволяет отслеживать действия конкретных групп пользователей, известных как когорты. Во время этого анализа предпринимается попытка вычисления пользы службы путем измерения количества возвращающихся пользователей. Пользователи группируются по времени первого использования службы. При анализе когорт нам необходимо найти снижение активности за первые отслеживаемые периоды. В названии каждой когорты обозначается неделя, в которую ее участники были замечены впервые.

В следующем примере анализируется количество действий, которые пользователи выполняют в течение 5 недель после первого использования службы.

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```
Результат этого примера должен быть следующим.

:::image type="content" source="images/samples/cohorts.png" alt-text="Выходные данные анализа когорт":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Обработка данных ежемесячных активных пользователей и удержание пользователей
В следующих примерах используется анализ временных рядов с функцией [series_fir](/azure/kusto/query/series-firfunction), позволяющей выполнять вычисления скользящего окна. Пример приложения, для которого выполняется мониторинг, — это интернет-магазин, отслеживающий активность пользователей через пользовательские события. Этот запрос отслеживает два типа действий пользователя (_AddToCart_ и _CheckOut_) и определяет _активных пользователей_, которые выполняли извлечения по крайней мере один раз в конкретный день.



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

Результат этого примера должен быть следующим.

:::image type="content" source="images/samples/rolling-mau.png" alt-text="Обработка ежемесячных выходных данных пользователей":::

В следующем примере приведенный выше запрос преобразуется в повторно используемую функцию и используется для вычисления пошагового прикрепления пользователя. Активными в этом запросе считаются пользователи, которые выполняли извлечения по крайней мере один раз в конкретный день.

``` Kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mvexpand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

Результат этого примера должен быть следующим.

:::image type="content" source="images/samples/user-stickiness.png" alt-text="Выходные данные закрепления пользователей":::

### <a name="regression-analysis"></a>Регрессионный анализ
В этом примере показано, как создать автоматическое средство обнаружения сбоев в работе службы исключительно на основе журналов трассировки приложения. Средство обнаружения ищет аномальное внезапное увеличение относительного количества ошибок и выполняет трассировку предупреждений в приложении.

Для оценки состояния службы на основе данных журналов трассировки используются два метода:

- [make-series](/azure/kusto/query/make-seriesoperator) — для преобразования полуструктурированных журналов текстовых сообщений в метрику, которая представляет собой соотношение между положительными и отрицательными линиями трассировки.
- [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) и [series_fit_line](/azure/kusto/query/series-fit-linefunction) — для выполнения расширенного обнаружения резкого перехода с использованием анализа временных рядов с помощью двухстрочной линейной регрессии.

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```


## <a name="next-steps"></a>Следующие шаги

- [Пошаговое руководство по языку запросов Kusto](tutorial.md?pivots=azuremonitor).


::: zone-end

