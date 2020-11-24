---
title: Примеры для запросов в Azure обозреватель данных и Azure Monitor
description: В этой статье описаны распространенные запросы и примеры использования языка запросов Kusto для Azure обозреватель данных и Azure Monitor.
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
ms.openlocfilehash: 866df577fa039e8b92c31753197cf4a4fa03f139
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783614"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Примеры запросов для обозреватель данных и Azure Monitor Azure

::: zone pivot="azuredataexplorer"

В этой статье приведены общие требования к запросам в обозреватель данных Azure, а также использование языка запросов Kusto для их удовлетворения.

## <a name="display-a-column-chart"></a>Отображение гистограммы

Для проецирования двух или более столбцов, а затем используйте столбцы в качестве оси x и оси y диаграммы:

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* В первом столбце образуется ось x. Это может быть числовой, Дата и время, или строка. 
* Используйте `where` , `summarize` и, `top` чтобы ограничить объем отображаемых данных.
* Отсортируйте результаты, чтобы определить порядок оси x.

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="Снимок экрана гистограммы с десятью цветными столбцами, отображающими соответствующие значения из 10 расположений.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Получение сеансов от событий начала и окончания

В журнале событий некоторые события отмечают начало или конец расширенного действия или сеанса. 

|Имя|Город|SessionId|Отметка времени|
|---|---|---|---|
|Начать|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Начать|Манчестер|4267667|2015-12-09T10:14:02.23|
|Остановить|London|2817330|2015-12-09T10:23:43.18|
|Отмена|Манчестер|4267667|2015-12-09T10:27:26.29|
|Остановить|Манчестер|4267667|2015-12-09T10:28:31.72|

Каждое событие имеет идентификатор сеанса ( `SessionId` ). Задача заключается в совпадении событий запуска и окончания с ИДЕНТИФИКАТОРом сеанса.

Пример.

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

Для сопоставления событий запуска и окончания с ИДЕНТИФИКАТОРом сеанса:

1. Используйте [let](./letstatement.md) , чтобы присвоить имя проекции таблицы, которая урезанные до того, как это возможно, перед началом объединения.
1. Используйте [проект](./projectoperator.md) , чтобы изменить имена меток времени, чтобы в результатах отображалось время начала и время окончания. `project` также выбирает другие столбцы для просмотра в результатах. 
1. Используйте параметр [Join](./joinoperator.md) , чтобы сопоставить записи запуска и окончания для одного и того же действия. Для каждого действия создается строка. 
1. Используйте `project` снова, чтобы добавить столбец для отображения длительности действия.

Выходные данные будут выглядеть следующим образом.

|City|SessionId|StartTime|StopTime|Длительность|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Манчестер|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>Получение сеансов без использования идентификатора сеанса

Предположим, что события запуска и завершения не имеют удобного идентификатора сеанса, который можно сопоставить с. Но у нас есть IP-адрес клиента, на котором выполнялся сеанс. Предполагая, что каждый клиентский адрес выполняет только один сеанс, мы можем сопоставить каждое событие запуска со следующим событием «завершение» с того же IP-адреса:

Пример.

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

`join`Соответствует каждому моменту времени начала со временем окончания одного и того же IP-адреса клиента. Пример кода:

- Удаляет совпадения с более ранними значениями времени окончания.
- Группы по времени начала и IP-адресу для получения группы для каждого сеанса. 
- Предоставляет `bin` функцию для `StartTime` параметра. Если вы не выполните этот шаг, Kusto автоматически использует одночасовые ячейки, которые соответствуют времени начала с неверным временем окончания.

`arg_min` находит строку с наименьшей длительностью в каждой группе, а `*` параметр проходит через все остальные столбцы. 

Префиксы аргументов `min_` для каждого имени столбца. 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="Снимок экрана таблицы, в которой перечислены результаты со столбцами времени начала, IP-адреса клиента, длительности, города и самой ранней остановкой для каждого сочетания &quot;клиент/время начала&quot;."::: 

Добавьте код для подсчета длительностей ячеек удобного размера. В этом примере из-за настройки линейчатой диаграммы выполните деление на, `1s` чтобы преобразовать интервалы времени в числа:

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="Снимок экрана гистограммы, отображающей количество сеансов с длительностью в указанных диапазонах.":::

### <a name="full-example"></a>Полный пример

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

Предположим, что у вас есть таблица действий и время их начала и окончания. Можно отобразить диаграмму, показывающую, сколько действий выполняется параллельно с течением времени.

Ниже приведен пример входных данных с именем `X` :

|SessionId | StartTime | StopTime |
|---|---|---|
| а | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| с | 10:03:02 | 10:05:20 |

Для диаграммы, которая находится в одной минутной ячейке, необходимо подсчитать каждое выполняемое действие с интервалом в одну минуту.

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

Вместо того чтобы хранить эти массивы, разверните их, используя [MV-Expand](./mvexpandoperator.md):

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

Теперь Сгруппируйте результаты по образцу времени и подсчитайте вхождения каждого действия:

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Используйте, `todatetime()` так как [MV-Expand](./mvexpandoperator.md) выдает столбец динамического типа.
* Используется, `bin()` поскольку для числовых значений и дат, если не указан интервал, `summarize` всегда применяет `bin()` функцию с использованием интервала по умолчанию. 

Выходные данные будут выглядеть следующим образом.

| count_SessionId | примеры|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Для визуализации результатов можно использовать линейчатую диаграмму или диаграмму.

## <a name="introduce-null-bins-into-summarize"></a>Ввод в *итоги* пустых ячеек

Если `summarize` оператор применяется к ключу группы, состоящему из столбца даты и времени, значения заносятся в ячейки фиксированной ширины:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

В этом примере создается таблица, которая содержит одну строку для каждой группы строк `T` , попадающие в каждую ячейку за пять минут.

В коде не добавляется "пустые ячейки" — строки для значений времени ячеек между `StartTime` и `StopTime` , для которых нет соответствующей строки в `T` . Рекомендуется «заполнять» таблицу этими ячейками. Вот один из способов:

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

Ниже приведено пошаговое описание предыдущего запроса. 

1. Используйте `union` оператор для добавления дополнительных строк в таблицу. Эти строки создаются `union` выражением.
1. `range`Оператор создает таблицу с одной строкой и столбцом. Таблица не используется ни для чего не для `mv-expand` работы.
1. `mv-expand`Оператор над `range` функцией создает столько строк, сколько в 5-минутных ячейках между `StartTime` и `EndTime` .
1. Используйте `Count` `0` .
1. `summarize`Оператор группирует ячейки из исходного (левого или внешнего) аргумента в `union` . Оператор также является ячейкой из внутреннего аргумента к нему (строки пустых ячеек). Этот процесс гарантирует, что выходные данные имеют одну строку на ячейку, значение которой равно нулю или является исходным числом.

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Получите больше данных, используя Kusto с машинным обучением 

Во многих интересных случаях используются алгоритмы машинного обучения и формируются интересные сведения из данных телеметрии. Часто для этих алгоритмов требуется строго структурированный набор данных в качестве их входных данных. Необработанные данные журнала обычно не соответствуют требуемой структуре и размеру. 

Начните с поиска аномалий в частоте ошибок определенной службы вывода Bing. Таблица журналов содержит 65 000 000 000 записей. В следующих основных фильтрах запросов 250 000 ошибок, а затем создается временный ряд ошибок, использующий функцию обнаружения аномалий [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Аномалии обнаруживаются службой Kusto и выделяются красными точками на диаграмме временных рядов.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Служба определила несколько временных контейнеров с подозрительными тарифами по ошибкам. Используйте Kusto для масштабирования этого периода. Затем выполните запрос, который выполняет статистическую обработку по `Message` столбцу. Попробуйте найти самые популярные ошибки. 

Соответствующие части всей трассировки стека сообщения обрезаются, поэтому результаты на странице лучше всего повышают. 

Вы можете увидеть успешную идентификацию первых восьми ошибок. Однако далее следует длинный ряд ошибок, так как сообщение об ошибке было создано с использованием строки формата, содержащей измененные данные:

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

На этом этапе использование `reduce` оператора помогает. Оператор определил 63 различных ошибок, порожденных в одной точке инструментирования трассировки в коде. `reduce` помогает сосредоточиться на дополнительных значимых трассировках ошибок в этом временном окне.

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
|  5112|Непредвиденная система вывода error..SysTEM. NullReferenceException: ссылка на объект не задает экземпляр объекта...
|  174|Инференцехостсервице Call failed..SysTEM. ServiceModel. CommunicationException: произошла ошибка при записи в канал:...
|  63|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. \* : Write \* для записи в объект начальника. \* : соЦиалграф. начальник. запросы...
|  10|Сбой Ексекутеалгорисммесод для метода "Рунциклефроминтеримдата"...
|  10|Ошибка системы вывода... Microsoft. Bing. Platform. вывод. Service. Managers. Усеринтеримдатаманажерексцептион:...
|  3|Инференцехостсервице вызов failed..SysTEM. ServiceModel. \* : объект, System. ServiceModel. Channels. \* + \* , для \* \* — это \* ... в пере...

Теперь у вас есть хорошее представление об основных ошибках, которые привели к обнаруженным аномалиям.

Чтобы понять последствия этих ошибок в образце системы, учитывайте следующее. 
* `Logs`Таблица содержит дополнительные многомерные данные, такие как `Component` и `Cluster` .
* Новый подключаемый модуль автокластера может помочь получить сведения о компоненте и кластере с помощью простого запроса. 

В следующем примере можно ясно увидеть, что каждая из четырех основных ошибок относится только к компоненту. Кроме того, хотя три основных ошибки относятся к кластеру DB4, четвертая ошибка происходит во всех кластерах.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Счетчик |Процент (%)|Компонент|Кластер|Сообщение
|---|---|---|---|---
|7125|26,64|инференцехостсервице|DB4|Ексекутеалгорисммесод для метода...
|7125|26,64|Неизвестный компонент|DB4|Сбой вызова Инференцехостсервице...
|7124|26,64|инференцеалгорисмексекутор|DB4|Непредвиденная системная ошибка вывода...
|5112|19,11|инференцеалгорисмексекутор|*|Непредвиденная системная ошибка вывода...

## <a name="map-values-from-one-set-to-another"></a>Сопоставьте значения из одного набора с другим

Распространенным вариантом использования запроса является статическое сопоставление значений. Статическое сопоставление позволяет улучшить представление результатов.

Например, в следующей таблице `DeviceModel` указывается модель устройства. Использование модели устройства не является удобной формой ссылки на имя устройства.  

|DeviceModel |Счетчик 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

 Использовать понятное имя более удобно:  

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

В следующих двух примерах показано, как изменить модель устройства на понятное имя для распознавания устройства.  

### <a name="map-by-using-a-dynamic-dictionary"></a>Отображение с помощью динамического словаря

Сопоставление можно реализовать с помощью динамического словаря и динамических методов доступа. Пример:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Dataset definition
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

### <a name="map-by-using-a-static-table"></a>Отображение с помощью статической таблицы

Можно также выполнить сопоставление с помощью постоянной таблицы и `join` оператора.
 
1. Создайте таблицу сопоставлений только один раз:

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. Создайте таблицу с содержимым устройства:

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5, 1 |iPhone 5 
    |iPhone3, 2 |iPhone 4 
    |iPhone7, 2 |iPhone 6 
    |iPhone5, 2 |iPhone5 

1. Создать тестовый источник таблицы:

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. Присоедините таблицы и запустите проект:

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

Выходные данные будут выглядеть следующим образом.

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Создание и использование таблиц измерений времени выполнения запросов

Часто требуется объединить результаты запроса с нерегламентированной таблицей измерения, которая не хранится в базе данных. Можно определить выражение, результатом которого является таблица, ограниченная одним запросом. 

Пример:

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

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>Получение последних записей (по метке времени) на удостоверение

Предположим, что у вас есть таблица, которая включает в себя следующее:
* `ID`Столбец, определяющий сущность, с которой связана каждая строка, например идентификатор пользователя или идентификатор узла.
* `timestamp`Столбец, предоставляющий ссылку времени для строки.
* Другие столбцы

Можно использовать [оператор TOP-Nested](topnestedoperator.md) , чтобы сделать запрос, возвращающий последние две записи для каждого значения `ID` столбца, где значение _Latest_ определяется как _`timestamp` имеющее наибольшее значение_:

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


Ниже приведено пошаговое описание предыдущего запроса (нумерация относится к числу, указанному в комментариях к коду):

1. `datatable`— Это способ создания некоторых тестовых данных для демонстрационных целей. Обычно здесь вы используете реальные данные.
1. Эта строка фактически означает _Возврат всех различных значений `id`_. 
1. Затем эта строка возвращает для двух первых записей, которые максимально подвышают:
     * `timestamp`Столбец
     * Столбцы предыдущего уровня (здесь просто `id` )
     * Столбец, указанный на этом уровне (здесь, `timestamp` )
1. Эта строка добавляет значения `bla` столбца для каждой записи, возвращенной предыдущим уровнем. Если таблица содержит другие интересующие вас столбцы, эту строку можно повторить для каждого из этих столбцов.
1. В последней строке для удаления "лишних" столбцов, которые появились в, используется [оператор размещения по проектам](projectawayoperator.md) `top-nested` .

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>Расширение таблицы в процентах от общего вычисления

Табличное выражение, включающее числовой столбец, более удобно для пользователя, когда оно сопровождается его значением в процентах от общего числа.

Например, предположим, что запрос создает следующую таблицу:

|сомесериес|сомеинт|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

Вы хотите отобразить таблицу следующим образом:

|сомесериес|сомеинт|Освобождаемый |
|----------|-------|----|
|Apple       |    100|33,3|
|Banana       |    200|66,6|

Чтобы изменить способ отображения таблицы, Вычислите сумму (SUM) `SomeInt` столбца, а затем разделите каждое значение этого столбца на общее. Для произвольных результатов используйте [оператор as](asoperator.md).

Пример:

```kusto
// The following table literally represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Apple",
  200, "Banana",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>Выполнение агрегатов в скользящем окне

В следующем примере показано, как суммировать столбцы с помощью скользящего окна. Для запроса используйте следующую таблицу, которая содержит цены на фруктам по меткам времени.

Вычислите минимальные, максимальные и суммарные затраты на каждый фруктов в день, используя скользящее окно из семи дней. Каждая запись в результирующем наборе объединяет предыдущие семь дней, а результаты содержат запись в день анализа.

Таблица фруктов:

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

Ниже приведен запрос агрегирования скользящего окна. См. описание после результата запроса.

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

Выходные данные будут выглядеть следующим образом.

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

Запрос растягивает (дублирует) каждую запись во входной таблице в течение семи дней после ее фактического отображения. Каждая запись на самом деле отображается семь раз. В результате ежедневное агрегирование включает все записи за предыдущие семь дней.


Ниже приведено пошаговое описание предыдущего запроса. 

1. Регистрировать каждую запись в один день (относительно `_start` ). 
1. Определите конец диапазона для каждой записи: `_bin + 7d` , если значение не выходит за пределы диапазона `_start` и `_end` , в этом случае, изменяется. 
1. Для каждой записи создайте массив из семи дней (меток времени), начиная с текущего дня записи. 
1. `mv-expand` массив, таким путем дублирование каждой записи в семь записей, один день друг за другом. 
1. Выполняйте статистическую функцию для каждого дня. Из-за #4 этот шаг фактически суммирует _за последние_ семь дней. 
1. Данные за первые семь дней являются неполными, так как в течение первых семи дней лукбакк период не используется. Первые семь дней исключаются из итогового результата. В этом примере они участвуют только в статистической обработке 2018-10-01.

## <a name="find-the-preceding-event"></a>Найти предыдущее событие

В следующем примере показано, как найти предыдущее событие между двумя наборами данных.  

У вас есть два набора данных: A и B. Для каждой записи в наборе данных B найдите предыдущее событие в наборе данных A (то есть `arg_max` запись в, которая по-прежнему _старше_ B).

Ниже приведены образцы наборов данных. 

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

Для этой проблемы мы рекомендуем два разных подхода. Вы можете протестировать оба конкретного набора данных, чтобы найти тот, который наиболее подходит для вашего сценария.

> [!NOTE] 
> Каждый подход может выполняться по-разному в разных наборах данных.

### <a name="approach-1"></a>Подход 1

Этот подход сериализует оба набора данных по ИДЕНТИФИКАТОРу и метке времени. Затем он группирует все события в наборе данных B со всеми предшествующими событиями в наборе данных A. Наконец, он выбирает из `arg_max` всех событий в наборе данных A в группе.

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

### <a name="approach-2"></a>Подход 2

Этот подход к решению проблемы требует максимального лукбакк периода. Этот подход просматривает, насколько _старая_ запись в наборе данных A может сравниваться с набором данных B. Затем метод соединяет два набора данных на основе идентификатора и лукбакк периода.

`join`Создает все возможные кандидаты, а все наборы данных — записи, которые старше записей в наборе данных B и в течение периода лукбакк. Затем ближайший элемент набора данных B фильтруется по `arg_min (TimestampB - TimestampA)` . Чем короче период лукбакк, тем лучше результаты запроса.

В следующем примере период лукбакк имеет значение `1m` . Запись с ИДЕНТИФИКАТОРом `z` не имеет соответствующего `A` события, так как его `A` событие старше двух минут.

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

|ID|B_Timestamp|A_Timestamp|евентб|Событие а|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|да|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|з|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>Дальнейшие действия

- Пошаговое [руководство по языку запросов Kusto](tutorial.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

В этой статье приведены общие требования к запросам в Azure Monitor и способы использования языка запросов Kusto для их удовлетворения.

## <a name="string-operations"></a>Операции со строками

В следующих разделах приведены примеры работы со строками при использовании языка запросов Kusto.

### <a name="strings-and-how-to-escape-them"></a>Строки и способ их экранирования

Строковые значения упаковываются как одинарные, так и двойные кавычки. Добавьте обратную косую черту ( \\ ) слева от символа для экранирования символа: `\t` для табуляции, `\n` для новой строки и `\"` для символа одинарной кавычки.

```kusto
print "this is a 'string' literal in double \" quotes"
```

```kusto
print 'this is a "string" literal in single \' quotes'
```

Чтобы символ \\ не интерпретировался как escape-символ, добавьте \@ в качестве префикса к строке:

```kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>Сравнения строк

Оператор       |Описание                         |С учетом регистра|Пример (при `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Равно                              |Да           |`"aBc" == "aBc"`
`!=`           |Не равно                          |Да           |`"abc" != "ABC"`
`=~`           |Равно                              |нет            |`"abc" =~ "ABC"`
`!~`           |Не равно                          |нет            |`"aBc" !~ "xyz"`
`has`          |Правое значение — это целый термин в значении слева |нет|`"North America" has "america"`
`!has`         |Значение справа не является полным условием в левой части значения       |нет            |`"North America" !has "amer"` 
`has_cs`       |Правое значение — это целый термин в значении слева |Да|`"North America" has_cs "America"`
`!has_cs`      |Значение справа не является полным условием в левой части значения       |Да            |`"North America" !has_cs "amer"` 
`hasprefix`    |Правое значение — префикс термина в значении слева.         |нет            |`"North America" hasprefix "ame"`
`!hasprefix`   |Значение справа не является префиксом термина в значении слева     |нет            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Правое значение — префикс термина в значении слева.         |Да            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Значение справа не является префиксом термина в значении слева     |Да            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Правое значение — это суффикс термина в левой части значения         |нет            |`"North America" hassuffix "ica"`
`!hassuffix`   |Значение правой части не является суффиксом в значении слева     |нет            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Правое значение — это суффикс термина в левой части значения         |Да            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Значение правой части не является суффиксом в значении слева     |Да            |`"North America" !hassuffix_cs "icA"`
`contains`     |Правое значение происходит как часть значения из левого края  |нет            |`"FabriKam" contains "BRik"`
`!contains`    |Значение правого края не встречается в значении слева           |нет            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Правое значение происходит как часть значения из левого края  |Да           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Значение правого края не встречается в значении слева           |Да           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Значение справа — это начальная последовательность значений левого края|нет            |`"Fabrikam" startswith "fab"`
`!startswith`  |Значение правой части не является начальной частью последовательности значений слева|нет        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Значение справа — это начальная последовательность значений левого края|Да            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Значение правой части не является начальной частью последовательности значений слева|Да        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Правое значение является закрывающей частью последовательности значений слева|нет             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Значение справа не является закрывающей частью значения левого края|нет         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Правое значение является закрывающей частью последовательности значений слева|Да             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Значение справа не является закрывающей частью значения левого края|Да         |`"Fabrikam" !endswith "brik"`
`matches regex`|Значение левого края содержит соответствие для правого значения|Да           |`"Fabrikam" matches regex "b.*k"`
`in`           |Соответствует одному из элементов       |Да           |`"abc" in ("123", "345", "abc")`
`!in`          |Не соответствует одному из элементов   |Да           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*каунтоф*

Подсчитывает количество вхождений подстроки в строке. Может сопоставлять простые строки или использовать регулярное выражение (Regex). Совпадение с обычными строками может перекрываться, но совпадения Regex не перекрываются.

```
countof(text, search [, kind])
```

- `text`: Входная строка 
- `search`: Простая строка или регулярное выражение для сопоставления внутри текста
- `kind`: _стандартное_  |  _регулярное выражение_ (по умолчанию: обычная).

Возвращает число совпадений строки поиска в контейнере. Совпадение с обычными строками может перекрываться, но совпадения Regex не перекрываются.

#### <a name="plain-string-matches"></a>Совпадения текстовой строки

```kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Совпадения регулярного выражения

```kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>*extract*

Возвращает соответствие для регулярного выражения из заданной строки. При необходимости может преобразовать извлеченную подстроку в указанный тип.

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`: Регулярное выражение.
- `captureGroup`: Положительная целочисленная константа, указывающая группу захвата для извлечения. Используйте значение 0 для всего совпадения, 1 для значения, совпадающего с первой круглой скобкой \( \) в регулярном выражении, и 2 или более для последующих скобок.
- `text` — Строка для поиска.
- `typeLiteral` — Необязательный литерал типа (например, `typeof(long)` ). Если указан, то извлеченная подстрока преобразуется в этот тип.

Возвращает подстроку, совпадающую с указанной группой захвата `captureGroup` , при необходимости преобразованную в `typeLiteral` . Если совпадений нет или преобразование типа завершается неудачно, возвращает значение null.

В следующем примере извлекается последний октет `ComputerIP` из записи пульса:

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

В следующем примере извлекается последний октет, приводится его к *реальному* типу (Number), а затем вычисляется следующее значение IP-адреса:

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

В следующем примере выполняется `Trace` Поиск определения в строке `Duration` . Совпадение приведено к `real` и умножается на константу времени (1 с), которая затем приводится `Duration` к типу `timespan` .

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*IsEmpty*, *иснотемпти* *,* вызрение

- `isempty` Возвращает `true` значение, если аргумент является пустой строкой или значением NULL (см `isnull` . раздел).
- `isnotempty` Возвращает `true` значение, если аргумент не является пустой строкой или значением NULL (см `isnotnull` . раздел). Псевдоним: `notempty` .


```kusto
isempty(value)
isnotempty(value)
```

#### <a name="example"></a>Пример

```kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>*парсеурл*

Разделяет URL-адрес на его части, такие как протокол, узел и порт, а затем возвращает объект Dictionary, содержащий части в виде строк.

```
parseurl(urlstring)
```

#### <a name="example"></a>Пример

```kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Выходные данные будут выглядеть следующим образом.

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

### <a name="replace"></a>*replace*

Заменяет все найденные совпадения регулярного выражения другой строкой. 

```
replace(regex, rewrite, input_text)
```

- `regex`: Регулярное выражение для сопоставления. Он может содержать группы записи в круглых скобках \( \) .
- `rewrite`: Регулярное выражение замены для любых совпадений, выполненных путем сопоставления регулярного выражения. Используйте \ 0 для ссылки на полное совпадение, \ 1 для первой группы захвата, \ 2 и т. д. для последующих групп записи.
- `input_text`: Входная строка для поиска.

Возвращает текст после замены всех совпадений Regex с вычислениями перезаписи. Совпадения не перекрываются.

#### <a name="example"></a>Пример

```kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Выходные данные будут выглядеть следующим образом.

Действие                                        |Замененный текст
------------------------------------------------|----------------------------------------------------------
4663 — была предпринята попытка доступа к объекту.  |Идентификатор действия 4663 — была предпринята попытка доступа к объекту.


### <a name="split"></a>*split*

Разделяет определенную строку в соответствии с заданным разделителем, а затем возвращает массив результирующих подстрок.

```
split(source, delimiter [, requestedIndex])
```

- `source`: Строка, которая должна быть разделена в соответствии с заданным разделителем.
- `delimiter`— Разделитель, который будет использоваться для разделения исходной строки.
- `requestedIndex`— Необязательный индекс, начинающийся с нуля. Если он предоставлен, то возвращаемый массив строк содержит только этот элемент (если он существует).


#### <a name="example"></a>Пример

```kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>*strcat*

Сцепляет строковые аргументы (поддерживает от 1 до 16 аргументов).

```
strcat("string1", "string2", "string3")
```

#### <a name="example"></a>Пример

```kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>*strlen*

Возвращает длину строки.

```
strlen("text_to_evaluate")
```

#### <a name="example"></a>Пример

```kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>*substring*

Извлекает подстроку из указанной исходной строки, начиная с указанного индекса. При необходимости можно указать длину запрашиваемой подстроки.

```
substring(source, startingIndex [, length])
```

- `source`: Исходная строка, из которой берется подстрока.
- `startingIndex`: Отсчитываемая от нуля начальная позиции запрашиваемой подстроки.
- `length`— Необязательный параметр, который можно использовать для указания запрошенной длины возвращаемой подстроки.

#### <a name="example"></a>Пример

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*ToLower*, *ToUpper*

Преобразует указанную строку в все символы нижнего или верхнего регистра.

```
tolower("value")
toupper("value")
```

#### <a name="example"></a>Пример

```kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>Операции с датой и временем

Следующие разделы содержат примеры работы со значениями даты и времени при использовании языка запросов Kusto.

### <a name="date-time-basics"></a>Основные сведения о дате и времени

Язык запросов Kusto имеет два основных типа данных, связанных с датами и временем: `datetime` и `timespan` . Все даты задаются в формате UTC. Хотя поддерживается несколько форматов даты и времени, рекомендуется использовать формат ISO-8601. 

Интервалы времени выражаются как десятичное число, за которым следует единица времени:

|Сокращения   | Единица времени    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | миллисекунда  |
|микросекунда | микросекунда  |
|галочка        | наносекунда   |

Значения даты и времени можно создать путем приведения строки с помощью `todatetime` оператора. Например, для просмотра пакетов пульса виртуальной машины, отправленных в определенное время, используйте `between` оператор, чтобы указать диапазон времени:

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Другой распространенный сценарий — сравнение значения даты и времени с текущим. Например, чтобы просмотреть все пакеты пульса за последние две минуты, можно использовать оператор `now` вместе с промежутком времени в две минуты:

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Для этой функции также доступно сокращение:

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Самый короткий и наиболее удобочитаемый метод использует `ago` оператор:

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Предположим, что вместо времени начала и окончания вы знаете время начала и длительность. Вы можете переписать запрос:

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>Преобразование единиц времени

Может потребоваться выразить значение даты-времени или TimeSpan в единицах времени, отличных от значения по умолчанию. Например, если вы просматриваете события ошибок за последние 30 минут и вам нужен вычисляемый столбец, показывающий, как долго произошло событие, можно использовать следующий запрос:

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo`Столбец содержит такие значения `00:09:31.5118992` , как, в формате чч: мм: СС. fffffff. Если вы хотите отформатировать эти значения в `number` минутах с момента начала, разделите это значение на `1m` :

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>Агрегирование и группирование по интервалам времени

Другой распространенный сценарий — необходимость получения статистики за определенный период времени в определенной единице времени. В этом сценарии оператор можно использовать `bin` как часть `summarize` предложения.

Используйте следующий запрос, чтобы получить количество событий, произошедших каждые пять минут за последние полугодия.

```kusto
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

Другим способом создания контейнеров результатов является использование таких функций, как `startofday` :

```kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Выходные данные будут выглядеть следующим образом.

|TIMESTAMP|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>Часовые пояса

Поскольку все значения даты и времени выражаются в формате UTC, часто бывает полезно преобразовать эти значения в местный часовой пояс. Например, используйте это вычисление для преобразования времени UTC в формат PST (тихоокеанское время):

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Агрегации

В следующих разделах приведены примеры статистической обработки результатов запроса при использовании языка запросов Kusto.

### <a name="count"></a>*count*

Выполните подсчет количества строк в результирующем наборе после применения фильтров. В следующем примере возвращается общее число строк в `Perf` таблице за последние 30 минут. Результаты возвращаются в столбце, имя `count_` которого указано, если не присвоить столбцу определенное имя:


```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Визуализация диаграмму может оказаться полезной для просмотра тенденций с течением времени:

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

В выходных данных этого примера показана `Perf` линия тренда числа записей с интервалом в пять минут:


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="Снимок экрана, на котором показана линия тренда числа записей о производительности за пять минут.":::

### <a name="dcount-dcountif"></a>*DCount*, *дкаунтиф*

Используйте `dcount` и `dcountif`, чтобы подсчитать уникальные значения в определенном столбце. Следующий запрос вычисляет, сколько отдельных компьютеров отправили пакеты пульса за последний час:

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Чтобы подсчитать только компьютеры Linux, отправлявшие пакеты пульса, используйте `dcountif`:

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluate-subgroups"></a>Вычислить подгруппы

Чтобы выполнить подсчет или другие агрегирования с подгруппами в данных, используйте ключевое слово `by`. Например, чтобы подсчитать количество отдельных компьютеров Linux, которые отправляли пакеты пульса в каждой стране или регионе, используйте следующий запрос:

```kusto
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


Чтобы проанализировать даже меньшие подгруппы данных, добавьте имена столбцов в `by` раздел. Например, может потребоваться подсчитать различные компьютеры из каждой страны или региона на тип операционной системы ( `OSType` ):

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>Процентиль

Чтобы найти медиану, используйте функцию `percentile` со значением, чтобы указать процентиль:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Вы также можете указать разные процентили, чтобы получить агрегированный результат для каждого из них:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Результаты могут показывать, что некоторые ЦП компьютера имеют похожие значения медианы. Однако несмотря на то, что некоторые компьютеры постоянно находятся в обычном направлении, другие выдавали больше значений ЦП. Высокое и низкое значения означают, что компьютеры имеют опытные пики.

### <a name="variance"></a>Variance

Чтобы напрямую рассчитать дисперсию значения, используйте стандартное отклонение и методы дисперсии:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Хорошим способом анализа стабильности использования ЦП является объединение `stdev` с помощью медианы вычислений:

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>Создание списков и наборов

Можно использовать `makelist` для сведения данных по порядку значений в определенном столбце. Например, может потребоваться изучить наиболее распространенные события заказов, выполняемые на компьютерах. Фактически данные можно сведениеить по порядку `EventID` значений на каждом компьютере: 

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

Выходные данные будут выглядеть следующим образом.

|Компьютер|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` формирует список в порядке, в котором были переданы данные. Чтобы отсортировать события от старых к новым, используйте `asc` `order` инструкцию вместо `desc` . 

Может оказаться полезным создать только список с разными значениями. Этот список называется _набором_, и его можно создать с помощью `makeset` команды:

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

Выходные данные будут выглядеть следующим образом.

|Компьютер|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Подобно `makelist` , `makeset` также работает с упорядоченными данными. `makeset`Команда создает массивы на основе порядка переданных строк.

### <a name="expand-lists"></a>Развернуть списки

Операция обратного `makelist` или `makeset` имеет значение `mv-expand` . `mv-expand`Команда расширяет список значений для разделения строк. Он может расширяться по любому числу динамических столбцов, включая JSON и столбцы массива. Например, можно проверить `Heartbeat` таблицу на наличие решений, которые отправили данные с компьютеров, которые отправили пульс за последний час:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

Выходные данные будут выглядеть следующим образом.

| Компьютер | Решения | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

Используйте `mv-expand` для отображения каждого значения в отдельной строке, а не в списке с разделителями-запятыми:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
```

Выходные данные будут выглядеть следующим образом.

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


Можно использовать `makelist` для группирования элементов. В выходных данных можно увидеть список компьютеров на решение:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

Выходные данные будут выглядеть следующим образом.

|Решения | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="missing-bins"></a>Отсутствующие ячейки

Полезное приложение `mv-expand` будет заполнять значения по умолчанию для отсутствующих ячеек. Например, предположим, что вы ищете время работы определенного компьютера, изучив его пульс. Кроме того, необходимо просмотреть источник пульса, который находится в `Category` столбце. Обычно используется базовая `summarize` Инструкция:

```kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

Выходные данные будут выглядеть следующим образом.

| Категория | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

В выходных данных контейнер, связанный с "2017-06-06T19:00:00Z", отсутствует, так как отсутствуют данные пульса за этот час. Используйте функцию `make-series`, чтобы присвоить значение по умолчанию пустым контейнерам. Для каждой категории создается строка. Выходные данные включают два дополнительных столбца массива: один для значений и один для совпадающих временных интервалов.

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

Выходные данные будут выглядеть следующим образом.

| Категория | count_ | TimeGenerated |
|---|---|---|
| Direct Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Третий элемент массива *count_* равен 0, как ожидалось. У массива _timegenerated_ есть соответствующая метка времени "2017-06-06T19:00:00.0000000 z". Но этот формат массива трудно читать. Используйте `mv-expand`, чтобы развернуть массивы и создать выходные данные того же формата, что и сгенерированные `summarize`:

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mv-expand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

Выходные данные будут выглядеть следующим образом.

| Категория | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T19:00:00Z | 0 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Ограничьте результаты до набора элементов: *let*, *make*, *тоскалар* *в*

Распространенный сценарий состоит в том, чтобы выбрать имена конкретных сущностей на основе набора критериев, а затем отфильтровать другой набор данных до этого набора сущностей. Например, можно найти компьютеры, на которых известно, что отсутствуют обновления, и определить IP-адреса, вызываемые этими компьютерами.

Пример:

```kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>Соединения

С помощью соединений можно анализировать данные из нескольких таблиц в одном запросе. Соединение объединяет строки двух наборов данных, сопоставляя значения указанных столбцов.

Пример:

```kusto
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

В этом примере первый фильтр набора данных фильтрует все события входа. Этот набор данных соединен с вторым набором данных, который фильтрует все события выхода. Проецируемые столбцы: `Computer` , `Account` , `TargetLogonId` и `TimeGenerated` . Наборы данных сопоставляются с помощью общего столбца `TargetLogonId` . Выходные данные представляют собой единственную запись для каждой корреляции, в которой есть время входа и выхода.

Если оба набора данных имеют столбцы с одинаковыми именами, то столбцам в правом наборе данных присваивается номер индекса. В этом примере результаты будут отображаться `TargetLogonId` со значениями из левой таблицы и `TargetLogonId1` со значениями из правой таблицы. В этом случае второй `TargetLogonId1` столбец был удален с помощью `project-away` оператора.

> [!NOTE]
> Для повышения производительности следует использовать оператор, чтобы обеспечить только соответствующие столбцы соединяемых наборов данных `project` .


Используйте следующий синтаксис для соединения двух наборов данных, в которых соединяемый ключ имеет другое имя между двумя таблицами:

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Таблицы подстановок

Обычно объединения используются `datatable` для сопоставления статических значений. Использование `datatable` позволяет улучшить представление результатов. Например, можно расширить данные событий безопасности с именем события для каждого идентификатора события:

```kusto
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

Выходные данные будут выглядеть следующим образом.

| eventName | count_ |
|:---|:---|
| Маркер объекта был закрыт | 290 995 |
| Запрошен маркер объекта | 154 157 |
| Предпринята попытка скопировать маркер в объект | 144 305 |
| Предпринята попытка доступа к объекту | 123 669 |
| Криптографическая операция | 153 495 |
| Операция с файлом ключа | 153 496 |

## <a name="json-and-data-structures"></a>JSON и структуры данных

Вложенные объекты — это объекты, содержащие другие объекты в массиве или на карте пар "ключ-значение". Объекты представлены в виде строк JSON. В этом разделе описывается, как можно использовать JSON для получения данных и анализа вложенных объектов.

### <a name="work-with-json-strings"></a>Работа со строками JSON

Используйте `extractjson`, чтобы получить доступ к определенному элементу JSON с известным путем. Для этой функции требуется выражение пути, которое использует следующие соглашения:

- Используйте _$_ для ссылки на корневую папку.
- Использование скобок или точечной нотации для обращения к индексам и элементам, как показано в следующих примерах.


Использование скобок для индексов и точек для разделения элементов:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Этот пример аналогичен, но он использует только нотацию квадратных скобок:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Только для одного элемента можно использовать только точечную нотацию:

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parseJSON*

Проще всего получить доступ к нескольким элементам в структуре JSON как к динамическому объекту. Используйте `parsejson` для приведения текстовых данных к динамическому объекту. После преобразования JSON в динамический тип можно использовать дополнительные функции для анализа данных.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```

### <a name="arraylength"></a>*arraylength*

Используйте `arraylength` для подсчета числа элементов в массиве:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mv-expand"></a>*mv-expand*

Используйте `mv-expand` для разбиения свойств объекта на отдельные строки:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="Снимок экрана показывает hosts_0 со значениями расположения, состояния и скорости.":::

### <a name="buildschema"></a>*буилдсчема*

Используйте `buildschema` для получения схемы, которая учитывает все значения объекта:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Результат представляет собой схему в формате JSON:

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

Схема описывает имена полей объектов и их совпадающих типов данных. 

Вложенные объекты могут иметь разные схемы, как показано в следующем примере:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Диаграммы

В следующих разделах приведены примеры работы с диаграммами при использовании языка запросов Kusto.

### <a name="chart-the-results"></a>Диаграмма результатов

Начните с просмотра числа компьютеров на операционную систему за последний час:

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

По умолчанию результаты отображаются в виде таблицы:

:::image type="content" source="images/samples/query-results-table.png" alt-text="Снимок экрана, показывающий результаты запроса в таблице.":::

Для получения более удобного представления выберите **Диаграмма**, а затем выберите вариант **круга** для визуализации результатов.

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="Снимок экрана, показывающий результаты запроса в круговой диаграмме.":::

### <a name="timecharts"></a>Временные диаграммы

Отображение среднего и 50 95 процентиль процентили времени процессора в ячейках за один час. 

Следующий запрос создает несколько рядов. В результатах можно выбрать ряд для отображения в диаграмму.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Выберите параметр **Показать график:**

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="Снимок экрана, на котором показан график с несколькими рядами.":::

#### <a name="reference-line"></a>Справочные данные

С помощью строки ссылки можно легко определить, превышает ли метрику определенное пороговое значение. Чтобы добавить линию в диаграмму, Расширьте набор данных, добавив постоянный столбец:

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="Снимок экрана, на котором показан график с несколькими рядами с пороговой строкой ссылки.":::


### <a name="multiple-dimensions"></a>Несколько измерений

Несколько выражений в `by` предложении `summarize` создают несколько строк в результатах. Для каждого сочетания значений создается одна строка.

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

При просмотре результатов в виде диаграммы в диаграмме используется первый столбец из `by` предложения. В следующем примере показана гистограмма с накоплением, созданная с использованием `EventID` значения. Размеры должны иметь `string` тип. В этом примере `EventID` значение приведено к `string` :

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="Снимок экрана, на котором показана линейчатая диаграмма на основе столбца EventID.":::

Можно переключаться между столбцами, щелкнув стрелку раскрывающегося списка для имени столбца:

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="Снимок экрана, на котором показана линейчатая диаграмма на основе столбца AccountType с видимым селектором столбцов.":::

## <a name="smart-analytics"></a>Смарт-аналитика

В этом разделе приведены примеры использования функций интеллектуального анализа в Azure Log Analytics для анализа активности пользователей. Эти примеры можно использовать для анализа собственных приложений, отслеживаемых Application Insights Azure, или использовать концепции в этих запросах для аналогичного анализа других данных. 

### <a name="cohorts-analytics"></a>Аналитика когорт

Анализ когорту отслеживает действия конкретных групп пользователей, известных как _когорты_. Когорту Analytics пытается оценить, насколько привлекательным является служба, измеряя частоту возвращения пользователей. Пользователи группируются по времени, в котором они впервые использовали службу. При анализе когорт нам необходимо найти снижение активности за первые отслеживаемые периоды. В названии каждой когорты обозначается неделя, в которую ее участники были замечены впервые.

В следующем примере анализируется количество действий, выполненных пользователями в течение пяти недель после их первого использования.

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
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

Выходные данные будут выглядеть следующим образом.

:::image type="content" source="images/samples/cohorts-table.png" alt-text="Снимок экрана, на котором показана таблица когорты на основе действия.":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Обработка данных ежемесячных активных пользователей и удержание пользователей

В следующем примере анализ временных рядов используется с функцией [series_fir](/azure/kusto/query/series-firfunction) . Функцию можно использовать `series_fir` для скользящего вычисления окна. Пример приложения, для которого выполняется мониторинг, — это интернет-магазин, отслеживающий активность пользователей через пользовательские события. Запрос отслеживает два типа действий пользователя: `AddToCart` и `Checkout` . Он определяет активного пользователя как пользователя, который выполнил извлечение по крайней мере один раз в конкретный день.

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

Выходные данные будут выглядеть следующим образом.

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="Снимок экрана диаграммы, показывающей число активных пользователей за день в месяц.":::

В следующем примере предыдущий запрос преобразуется в повторно используемую функцию. Затем в примере используется запрос для вычисления пошаговой пользовательской прикрепления. Активный пользователь в этом запросе определяется как пользователь, который выполнил извлечение по крайней мере один раз в определенный день.

```kusto
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
    | mv-expand RollingUserClicksByDay
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

Выходные данные будут выглядеть следующим образом.

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="Снимок экрана диаграммы, показывающей пользовательское прикрепление с течением времени.":::

### <a name="regression-analysis"></a>Регрессионный анализ

В этом примере показано, как создать автоматическое средство обнаружения сбоев в работе службы исключительно на основе журналов трассировки приложения. Средство обнаружения ненормальное, внезапно увеличивает относительный объем трассировки ошибок и предупреждений в приложении.

Для оценки состояния службы на основе данных журналов трассировки используются два метода:

- [make-series](/azure/kusto/query/make-seriesoperator) — для преобразования полуструктурированных журналов текстовых сообщений в метрику, которая представляет собой соотношение между положительными и отрицательными линиями трассировки.
- Используйте [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) и [series_fit_line](/azure/kusto/query/series-fit-linefunction) для расширенного обнаружения переходов по шагам с помощью анализа временных рядов и двухсторонней линейной регрессии.

```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>Дальнейшие действия

- Пошаговое [руководство по языку запросов Kusto](tutorial.md?pivots=azuremonitor).


::: zone-end

