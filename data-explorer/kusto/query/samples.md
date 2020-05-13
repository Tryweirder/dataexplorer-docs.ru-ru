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
ms.openlocfilehash: fe44323dabb246438f18c9ab01eec0008ad4fe97
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372970"
---
# <a name="samples"></a>Примеры

Ниже приведены некоторые распространенные потребности в запросах, а также способы использования языка запросов Kusto для их удовлетворения.

## <a name="display-a-column-chart"></a>Отображение гистограммы

Выполните проект с двумя или более столбцами и используйте их в качестве оси x и y диаграммы:

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* В первом столбце образуется ось x. Это может быть числовой, DateTime или String. 
* Используйте `where` `summarize` и `top` для ограничения объема отображаемых данных.
* Отсортируйте результаты, чтобы определить порядок оси x.

:::image type="content" source="images/samples/060.png" alt-text="060":::

<a name="activities"></a>
## <a name="get-sessions-from-start-and-stop-events"></a>Получение сеансов от событий начала и окончания

Предположим, что у нас есть журнал событий, в котором некоторые события отмечают начало или конец расширенного действия или сеанса. 

|Имя|Город|SessionId|Отметка времени|
|---|---|---|---|
|Запуск|London|2817330|2015-12-09T10:12:02.32|
|Игра|London|2817330|2015-12-09T10:12:52.45|
|Запуск|Манчестер|4267667|2015-12-09T10:14:02.23|
|Остановить|London|2817330|2015-12-09T10:23:43.18|
|Отмена|Манчестер|4267667|2015-12-09T10:27:26.29|
|Остановить|Манчестер|4267667|2015-12-09T10:28:31.72|

У каждого события есть идентификатор сеанса SessionId, поэтому задача состоит в том, чтобы сопоставить события начала и конца с одинаковыми идентификаторами.

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

Используйте [`let`](./letstatement.md) для именования проекции таблицы, которая урезанные вниз до перехода в соединение.
[`Project`](./projectoperator.md)используется для изменения имен меток времени, чтобы в результате могли появляться и время запуска, и значение времени окончания. Также выбираются другие столбцы, которые мы бы хотели видеть в результатах. [`join`](./joinoperator.md)совпадает с записями начала и окончания для одного и того же действия, создавая строку для каждого действия. Наконец, `project` снова добавляет столбец для отображения длительности действия.


|Город|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Манчестер|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>Получение сеансов без идентификатора сеанса

Теперь предположим, что у событий начала и окончания нет идентификатора сеанса, который мы можем использовать. Но у нас есть IP-адрес клиента, на котором выполнялся сеанс. Если каждый клиент проводит только один сеанс одновременно, то можно сопоставить каждое событие начала следующему событию окончания с того же IP-адреса.

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

Соединение будет сопоставлять каждое событие начала всем событиям окончания с того же IP-адреса клиента. Поэтому сначала удалим соответствия с более ранним временем окончания.

Затем мы сгруппированы по времени начала и IP, чтобы получить группу для каждого сеанса. Необходимо предоставить `bin` функцию для параметра StartTime: если мы не будем, Kusto будет автоматически использовать 1-часовой интервал, который будет соответствовать времени начала с неверным временем окончания.

`arg_min`выбирает строку с наименьшей длительностью в каждой группе, а `*` параметр передается по всем остальным столбцам, хотя он добавляет префикс "min_" к каждому имени столбца. 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

Затем мы можем добавить код для подсчета длительностей в ячейках с удобным размером. Мы сделали незначительную настройку для линейчатой диаграммы, так что мы разделены `1s` на, чтобы преобразовать интервалы времени в числа. 


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 

:::image type="content" source="images/samples/050.png" alt-text="050":::

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

<a name="concurrent-activities"><a/>
## <a name="chart-concurrent-sessions-over-time"></a>Диаграммы для параллельных сеансов

Предположим, что у нас есть таблица действий с временами их начала и окончания.  Мы бы хотели просмотреть диаграмму, которая показывает, сколько сеансов выполняется параллельно в любой момент времени.

Ниже приведен пример входных данных, которые мы будем называть `X`:

|SessionId | StartTime | StopTime |
|---|---|---|
| а | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| с | 10:03:02 | 10:05:20 |

Мы хотим получить диаграмму с группированием по 1 минуте, поэтому хотим создать что-то такое, что можно подсчитывать с интервалом 1 минута для каждого выполняющегося действия.

Вот промежуточный результат:

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` создает массив значений в заданных интервалах времени:

|SessionId | StartTime | StopTime  | примеры|
|---|---|---|---|
| а | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| с | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Но вместо того, чтобы хранить эти массивы, мы расширяем их с помощью [MV-Expand](./mvexpandoperator.md):

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

Теперь можно сгруппировать их по времени выборки, подсчитав количество вхождений для каждого действия:

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Нам нужно ToDateTime (), так как [MV-Expand](./mvexpandoperator.md) возвращает столбец динамического типа.
* Нам нужно использовать функцию bin(), так как для числовых значений и дат при обработке статистических данных всегда применяется функция группировки с интервалом по умолчанию, если интервал не указан. 


| count_SessionId | примеры|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Это можно отобразить в виде линейчатой или временной диаграммы.

## <a name="introduce-null-bins-into-summarize"></a>Ввод в итоги пустых ячеек

Когда `summarize` оператор применяется к ключу группы, состоящему из `datetime` столбца, один обычно «ячейки» этих значений заносятся в ячейки фиксированной ширины. Например:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Эта операция создает таблицу с одной строкой для каждой группы строк `T` , попадающие в каждую ячейку за пять минут. В этом случае добавляются "пустые ячейки" — строки для значений времени ячеек между `StartTime` и `StopTime` для которых нет соответствующей строки в `T` . 

Часто нужно «заполнять» таблицу этими ячейками. Вот один из способов:

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

1. Использование `union` оператора позволяет нам добавлять в таблицу дополнительные строки. Эти строки создаются выражением для `union` .
2. Использование `range` оператора для создания таблицы с одной строкой или столбцом.
   Таблица не используется ни для чего не для `mv-expand` работы.
3. Использование `mv-expand` оператора `range` для функции, чтобы создать столько строк, сколько 5-минутных ячеек между `StartTime` и `EndTime` .
4. Все с `Count` `0` .
5. Наконец, мы используем `summarize` оператор для группирования ячеек из исходного (левого или внешнего) аргумента в `union` и ячейки из внутреннего аргумента в него (а именно строки с пустыми ячейками). Это гарантирует, что выходные данные имеют одну строку на ячейку, значение которой равно нулю или является исходным числом.  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>Получите больше данных в Kusto с помощью Машинное обучение 

Существует много интересных вариантов использования алгоритмов машинного обучения и получения интересных ценных сведений из данных телеметрии. Хотя часто эти алгоритмы требуют наличия в качестве входных данных очень структурированного набора данных, необработанные данные журнала обычно не соответствуют требуемой структуре и размеру. 

Наше путешествие начинается с поиска аномалий в частоте ошибок определенной службы вывода Bing. Таблица LOGS содержит записи 65B, а простой запрос, приведенный ниже, фильтрует ошибки 250 000 и создает данные временных рядов из числа ошибок, в которых используется функция обнаружения аномалий [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Аномалии обнаруживаются службой Kusto и выделяются красными точками на диаграмме временных рядов.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Служба определила несколько временных контейнеров с подозрительными частотами ошибок. Я использую Kusto для масштабирования этого промежутка времени, запуская запрос, который выполняет статистическую обработку в столбце Message, пытаясь найти наиболее частые ошибки. Я обрезаю нужные части из всей трассировки стека сообщения, чтобы лучше разместить ее на странице. Вы видите, что у меня было удачное завершение первых восьми ошибок, но затем достигнут длинный заключительный фрагмент ошибок, так как сообщение об ошибке было создано строкой формата, содержащей изменяемые данные. 

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
|7125|Сбой вызова Инференцехостсервице.. System. NullReferenceException: ссылка на объект не задает экземпляр объекта...
|7124|Непредвиденная системная ошибка вывода.. System. NullReferenceException: ссылка на объект не задает экземпляр объекта... 
|5112|Непредвиденная системная ошибка вывода.. System. NullReferenceException: ссылка на объект не задает экземпляр объекта..
|174|Сбой вызова Инференцехостсервице.. System. ServiceModel. CommunicationException: произошла ошибка при записи в канал:...
|10|Сбой Ексекутеалгорисммесод для метода "Рунциклефроминтеримдата"...
|10|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. Service. Managers. Усеринтеримдатаманажерексцептион:...
|3|Сбой вызова Инференцехостсервице.. System. ServiceModel. Коммуникатионобжектфаултедексцептион:...
|1|Ошибка системы вывода... СоЦиалграф. начальник. Оператионреспонсе... AIS TraceId: 8292FC561AC64BED8FA243808FE74EFD...
|1|Ошибка системы вывода... СоЦиалграф. начальник. Оператионреспонсе... AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF...

Именно здесь оператор может `reduce` помочь. `reduce`Оператор определил 63 различных ошибок, созданных одной и той же точкой инструментирования трассировки в коде, и помог мне сосредоточиться на дополнительной осмысленной трассировке ошибок в этом временном окне.

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
|  7125|Сбой вызова Инференцехостсервице.. System. NullReferenceException: ссылка на объект не задает экземпляр объекта...
|  7124|Непредвиденная системная ошибка вывода.. System. NullReferenceException: ссылка на объект не задает экземпляр объекта...
|  5112|Непредвиденная системная ошибка вывода.. System. NullReferenceException: ссылка на объект не задает экземпляр объекта..
|  174|Сбой вызова Инференцехостсервице.. System. ServiceModel. CommunicationException: произошла ошибка при записи в канал:...
|  63|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. \* : Write \* для записи в объект начальника. \* : соЦиалграф. начальник. запросы...
|  10|Сбой Ексекутеалгорисммесод для метода "Рунциклефроминтеримдата"...
|  10|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. Service. Managers. Усеринтеримдатаманажерексцептион:...
|  3|Сбой вызова Инференцехостсервице.. System. ServiceModel. \* : объект, System. ServiceModel. Channels. \* + \* , для \* \* — это \* ...   в пере...

Теперь, когда у меня есть хорошее представление об основных ошибках, которые привели к обнаруженным аномалиям, я хочу понять последствия этих ошибок в моей системе. Таблица LOGS содержит дополнительные многомерные данные, такие как "Component", "Cluster" и т. д. Новый подключаемый модуль "автокластер" поможет вам получить представление о простом запросе. В этом примере ниже я могу ясно увидеть, что каждая из четырех первых ошибок относится к компоненту, а первые три ошибки относятся к кластеру DB4, четвертый из них выполняется во всех кластерах.

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

## <a name="mapping-values-from-one-set-to-another"></a>Сопоставление значений из одного набора с другим

Типичный вариант использования — это статическое сопоставление значений, которое может помочь в применении результатов к более подступающему способу.  
Например, рассмотрим наличие следующей таблицы. DeviceModel указывает модель устройства, которая не является очень удобной формой ссылки на имя устройства.  


|DeviceModel |Счетчик 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

  
Лучшее представление может быть следующим:  

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Эти два подхода демонстрируют, как это может быть достигнуто.  

### <a name="mapping-using-dynamic-dictionary"></a>Сопоставление с использованием динамического словаря

Приведенный ниже подход показывает, как можно достичь сопоставления с помощью динамического словаря и динамических методов доступа.

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



### <a name="mapping-using-static-table"></a>Сопоставление с использованием статической таблицы

Приведенный ниже подход показывает, как можно достичь сопоставления с помощью постоянной таблицы и оператора Join.
 
Создайте таблицу сопоставления (только один раз):

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Теперь содержимое устройств:

|DeviceModel |FriendlyName 
|---|---
|iPhone5, 1 |iPhone 5 
|iPhone3, 2 |iPhone 4 
|iPhone7, 2 |iPhone 6 
|iPhone5, 2 |iPhone5 


Такой же прием для создания источника тестовой таблицы:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


Присоединение и проект:

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

Результат: ;

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="creating-and-using-query-time-dimension-tables"></a>Создание и использование таблиц измерений времени выполнения запросов

Во многих случаях один из них хочет объединить результаты запроса с некоторой таблицей нерегламентированного измерения, которая не хранится в базе данных. Можно определить выражение, результатом которого является таблица, ограниченная одним запросом, следующим образом:

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

Вот немного более сложный пример:

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>Получение последних записей (по метке времени) на удостоверение

Предположим, что имеется таблица, содержащая `id` столбец (определяющий сущность, с которой связана каждая строка, например идентификатор пользователя или идентификатор узла) и `timestamp` столбец (предоставляющий ссылку времени для строки), а также другие столбцы. Ваша цель — написать запрос, возвращающий последние 2 записи для каждого значения `id` столбца, где "latest" определен как "с наибольшим значением `timestamp` ".

Это можно сделать с помощью [оператора TOP-Nested](topnestedoperator.md).
Сначала мы предоставляем запрос, а затем объясню его:

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // (1)
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // (2)
  top-nested 2 of timestamp by dummy1=max(timestamp),          // (3)
  top-nested   of bla       by dummy2=max(1)                   // (4)
| project-away dummy0, dummy1, dummy2                          // (5)
```

Примечания
1. `datatable`— Это просто способ создания некоторых тестовых данных для демонстрационных целей. В реальности, конечно, у вас есть данные.
2. Эта строка по сути означает «возврат всех различных значений `id` ».
3. Затем эта строка возвращает, для первых двух записей, которые развернут `timestamp` столбец, столбцы предыдущего уровня (в данном случае просто `id` ) и столбец, указанный на этом уровне (здесь `timestamp` ).
4. Эта строка добавляет значения `bla` столбца для каждой записи, возвращенной предыдущим уровнем. Если таблица содержит другие интересующие столбцы, одна из этих строк будет повторена для каждого такого столбца.
5. Наконец, мы используем [оператор размещения по проекту](projectawayoperator.md) для удаления "лишних" столбцов, введенных `top-nested` .

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>Расширение таблицы при расчете процента от общего числа

Часто, если у одной таблицы есть табличное выражение, включающее числовой столбец, то желательно, чтобы этот столбец был доступен пользователю, рядом с его значением в процентах от общего числа. Например, предположим, что существует запрос, значение которого является следующей таблицей.

|сомесериес|сомеинт|
|----------|-------|
|Foo       |    100|
|линейчатая диаграмма.       |    200|

И вы хотите отобразить эту таблицу как:

|сомесериес|сомеинт|Освобождаемый |
|----------|-------|----|
|Foo       |    100|33,3|
|линейчатая диаграмма.       |    200|66,6|

Для этого необходимо вычислить сумму (SUM) `SomeInt` столбца, а затем разделить каждое значение этого столбца на общее. Это можно сделать для произвольных результатов, присвоив этим результатам имя с помощью [оператора as](asoperator.md):

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

## <a name="performing-aggregations-over-a-sliding-window"></a>Выполнение агрегатов в скользящем окне
В следующем примере показано, как суммировать столбцы с помощью скользящего окна. Позволяет использовать, например, таблицу ниже, которая содержит цены фруктам по меткам времени. Предположим, мы хотели бы вычислить минимальную, максимальную и общую стоимость каждого числа фруктов в день, используя скользящее окно из 7 дней. Иными словами, каждая запись в результирующем наборе объединяет последние семь дней, а результат содержит запись в день анализа.  

Таблица фруктам: 

|Отметка времени|Фрукт|Price|
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

Запрос агрегирования скользящего окна (пояснения приведены ниже результатов запроса): 

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

Запрос "растягивает" (дубликаты) Каждая запись во входной таблице в течение 7 дней проводит свое фактическое представление, чтобы каждая запись на самом деле появилась 7 раз. В результате, при выполнении статистической обработки в каждый день, Статистическая обработка включает все записи за предыдущие семь дней.

Пошаговое описание (числа см. в разделе Комментарии к встроенным запросам):
1. Регистрировать каждую запись в 1D (относительно _start). 
2. Определите конец диапазона на запись (_bin + 7D), если это не выходит за пределы диапазона _(начальный, конечный)_ , в этом случае он корректируется. 
3. Для каждой записи создайте массив из 7 дней (меток времени), начиная с текущего дня записи. 
4. MV — расширение массива, таким путем дублирование каждой записи в 7 записей, один день друг за другом. 
5. Выполняйте статистическую функцию для каждого дня. Из-за #4 на самом деле приводится сводка за _последние_ семь дней. 
6. Наконец, поскольку данные для первого 7D не являются полными (нет 7D лукбакк период в течение первых семи дней), мы исключаем первые семь дней из окончательного результата (они участвуют только в статистической обработке 2018-10-01). 

## <a name="find-preceding-event"></a>Найти предыдущее событие
В следующем примере показано, как найти предыдущее событие между двумя наборами данных.  

*Назначение:* . при наличии 2 наборов данных A и B для каждой записи в B находит предшествующее событие в (иными словами, запись arg_max в, которая по-прежнему является "более старой", чем B). Например, ниже приведен ожидаемый результат для следующих образцов наборов данных: 

```kusto
let A = datatable(Timestamp:datetime, Id:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, Id:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Отметка времени|Идентификатор|евентб|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|Отметка времени|Идентификатор|Событие а|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

Ожидаемые выходные данные: 

|Идентификатор|Отметка времени|евентб|A_Timestamp|Событие а|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Для этой проблемы предлагается 2 различных подхода. Необходимо протестировать оба в конкретном наборе данных, чтобы найти один из наиболее подходящих для вас (они могут работать по-разному в разных наборах данных). 

### <a name="suggestion-1"></a>#1 предложения:
В этом предложении оба набора данных сериализуются по идентификатору и метке времени, затем группируют все события (B) со всеми предшествующими событиями и выбирают `arg_max` все, как в группе. 

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by Id, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != Id), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, Id
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>#2 предложения:
В этом предложении требуется определить параметр max-лукбакк-period (насколько "старая", чтобы можно было сравнить запись в с с B?), а затем присоединить 2 набора данных по идентификатору и этому лукбакк периоду. Соединение создает все возможные кандидаты (все записи, которые старше B и в период лукбакк), а затем фильтрует ближайшее значение в B, arg_min (Тиместампб — метка времени). Чем меньше период лукбакк, тем более производительен запрос. 

В приведенном ниже примере для периода лукбакк задано значение 1м, поэтому запись с идентификатором "z" не имеет соответствующего события "A" (поскольку значение "A" старше 1 МБ).

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
) on Id, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project Id, B_Timestamp, A_Timestamp, EventB, EventA
```

|Идентификатор|B_Timestamp|A_Timestamp|евентб|Событие а|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||
