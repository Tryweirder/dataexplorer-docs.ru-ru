---
title: Примеры для Azure обозреватель данных
description: В этой статье описываются примеры в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bc28fcb860dc067d55dd2e5ce9de3f3a17b402f2
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942324"
---
# <a name="samples"></a>Примеры

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
|Стоп|London|2817330|2015-12-09T10:23:43.18|
|Отмена|Манчестер|4267667|2015-12-09T10:27:26.29|
|Стоп|Манчестер|4267667|2015-12-09T10:28:31.72|

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

:::image type="content" source="images/samples/040.png" alt-text="Снимок экрана гистограммы. Ось y лежит в диапазоне от 0 до примерно 50. В десяти цветных столбцах показаны соответствующие значения в 10 расположениях."::: 

Добавьте код для подсчета длительностей ячеек удобного размера. В этом примере из-за настройки линейчатой диаграммы разделите на, `1s` чтобы преобразовать интервалы времени в числа. 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="Снимок экрана гистограммы. Ось y лежит в диапазоне от 0 до примерно 50. В десяти цветных столбцах показаны соответствующие значения в 10 расположениях.":::

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
| c | 10:03:02 | 10:05:20 |

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
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

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
| c | 10:03:12 | 10:04:30 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|

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

|Счетчик|Шаблон
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
|2019-01-01 00:00:03.0000000|x|С|
|2019-01-01 00:00:04.0000000|x|С|
|2019-01-01 00:00:04.0000000|да|С|
|2019-01-01 00:02:00.0000000|з|С|

Ожидаемые выходные данные: 

|ID|Отметка времени|евентб|A_Timestamp|Событие а|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|С|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|С|2019-01-01 00:00:01.0000000|Ax2|
|да|2019-01-01 00:00:04.0000000|С|2019-01-01 00:00:02.0000000|Ay1|
|з|2019-01-01 00:02:00.0000000|С|2019-01-01 00:00:00.0000000|Az1|

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
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|С|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|С|Ax2|
|да|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|С|Ay1|
|з|2019-01-01 00:02:00.0000000||С||
