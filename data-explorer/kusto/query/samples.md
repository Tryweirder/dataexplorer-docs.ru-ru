---
title: Примеры запросов в Azure Data Explorer и Azure Monitor
description: В этой статье описаны распространенные запросы и приведены примеры использования языка запросов Kusto для Azure Data Explorer и Azure Monitor.
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
ms.openlocfilehash: 4da3a7137b170b8048fbf03b6e6b6369eb4785d9
ms.sourcegitcommit: 62eff65b320ce4ca53eabed6156eb9fe5b77f548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224236"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Примеры запросов для Azure Data Explorer и Azure Monitor

::: zone pivot="azuredataexplorer"

В этой статье приведены общие требования к запросам в Azure Data Explorer и показано, как обеспечить их соблюдение с помощью языка запросов Kusto.

## <a name="display-a-column-chart"></a>Отображение гистограммы

Для проецирования двух или более столбцов и дальнейшего использования их в качестве осей X и Y диаграммы выполните следующие действия.

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* Первый столбец образует ось X. Он может содержать числа, дату и время или строки. 
* Чтобы ограничить объем отображаемых данных, используйте `where`, `summarize` и `top`.
* Отсортируйте результаты, чтобы определить порядок отображения на оси X.

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="Снимок экрана, на котором показана гистограмма с десятью цветными столбцами, обозначающими соответствующие значения из 10 расположений.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Получение сеансов от событий начала и окончания

Некоторые события отмечают начало или конец расширенного действия или сеанса в журнале событий. 

|Имя|Город|SessionId|Отметка времени|
|---|---|---|---|
|Начать|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Начать|Манчестер|4267667|2015-12-09T10:14:02.23|
|Остановить|London|2817330|2015-12-09T10:23:43.18|
|Отменить|Манчестер|4267667|2015-12-09T10:27:26.29|
|Остановить|Манчестер|4267667|2015-12-09T10:28:31.72|

Каждое событие имеет идентификатор сеанса (`SessionId`). Задача заключается в сопоставлении событий начала и окончания с идентификатором сеанса.

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

Сопоставление событий начала и окончания с идентификатором сеанса

1. Перед выполнением соединения присвойте проекции максимально упрощенной таблицы имя с помощью инструкции [let](./letstatement.md).
1. Используйте [project](./projectoperator.md) для изменения имен временных меток, чтобы значения времени начала и окончания отображались в результатах. С помощью `project` также можно выбрать другие столбцы для отображения в результатах. 
1. Чтобы сопоставить записи начала и окончания для одного и того же действия, используйте [join](./joinoperator.md). Для каждого действия создается строка. 
1. Используйте `project` снова, чтобы добавить столбец для отображения длительности действия.

Выходные данные будут выглядеть следующим образом.

|City|SessionId|StartTime|StopTime|Длительность|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Манчестер|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>Получение сеансов без их идентификатора

Предположим, что у событий начала и окончания нет идентификатора сеанса для сопоставления. Однако есть IP-адрес клиента, на котором выполнялся сеанс. Если каждый клиент проводит только один сеанс одновременно, то можно сопоставить каждое событие начала следующему событию окончания с того же IP-адреса.

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

Оператор `join` будет сопоставлять каждое значение времени начала со всеми значениями времени окончания с того же IP-адреса клиента. Пример кода выполняет следующие действия:

- Удаляет соответствия с более ранними значениями времени окончания.
- Группирует данные по времени начала и IP-адресу, чтобы получить группу для каждого сеанса. 
- Предоставляет функцию `bin` для параметра `StartTime`. Если вы не выполните этот шаг, Kusto автоматически использует одночасовые интервалы, которые сопоставят значения времени начала с неверными значениями времени окончания.

`arg_min` находит строку с наименьшей длительностью в каждой группе, а с помощью параметра `*` передаются все остальные столбцы. 

Аргумент добавляет `min_` перед именем каждого столбца. 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="Снимок экрана таблицы, в которой перечислены результаты со столбцами для времени начала, IP-адреса клиента, длительности, города и самого раннего времени окончания для каждого сочетания клиента и времени начала."::: 

Добавьте код для подсчета длительности интервалами удобного размера. Поскольку в этом примере используется гистограмма, для преобразования временных интервалов в числа показатели делятся на `1s`.

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="Снимок экрана гистограммы, на которой показано количество сеансов с длительностью в указанных диапазонах.":::

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

Предположим, что у нас есть таблица действий и времена их начала и окончания. Вы можете отобразить диаграмму, на которой будет показано, сколько действий выполняется параллельно с течением времени.

Ниже приведен пример входных данных с именем `X`.

|SessionId | StartTime | StopTime |
|---|---|---|
| а | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| с | 10:03:02 | 10:05:20 |

Для диаграммы с одноминутными интервалами, необходимо подсчитывать все выполняемые действия с интервалом в одну минуту.

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

Вместо хранения этих массивов, разверните их с помощью [mv-expand](./mvexpandoperator.md):

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

Теперь сгруппируйте результаты по времени выборки и подсчитайте количество вхождений для каждого действия:

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count_SessionId = count() by bin(todatetime(samples),1m)
```

* Используйте `todatetime()`, так как [mv-expand](./mvexpandoperator.md) выдает столбец динамического типа.
* Используйте функцию `bin()`, так как для числовых значений и дат `summarize` всегда применяет функцию `bin()` с интервалом по умолчанию, если интервал не указан. 

Выходные данные будут выглядеть следующим образом.

| count_SessionId | примеры|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Для преобразования результатов для просмотра можно использовать линейчатую или временную диаграмму.

## <a name="introduce-null-bins-into-summarize"></a>Введения пустых интервалов в *summarize*

Если оператор `summarize` применяется к ключу группы, состоящему из столбца даты и времени, значения заносятся в интервалы фиксированной ширины.

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

В этом примере создается таблица с одной строкой для группы строк в `T`, которые приходятся на каждый пятиминутный интервал.

При этом код не добавляет "пустые интервалы" (строки для значений временных интервалов между `StartTime` и `StopTime`, для которых в `T` нет соответствующей строки). Однако таблицу рекомендуется "заполнять" этими интервалами. Это можно сделать одним из следующих способов:

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

1. Для добавления дополнительных строк в таблицу воспользуйтесь оператором `union`. Эти строки создаются выражением `union`.
1. Оператор `range` создает таблицу с одной строкой и одним столбцом. Таблица используется исключительно для применения `mv-expand`.
1. Оператор `mv-expand`, применяемый к функции `range` создает строки, число которых равно количеству пятиминутных интервалов между `StartTime` и `EndTime`.
1. Используйте для `Count` значение `0`.
1. Оператор `summarize` группирует интервалы из исходного (левого или внешнего) аргумента в `union`. Этот оператор также добавляет интервалы из внутреннего аргумента (строки для пустых интервалов). Этот процесс гарантирует, что выходные данные будут содержать одну строку для каждого интервала, значение которой равно нулю или исходному количеству.

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Эффективное использование данных с помощью Kusto с машинным обучением 

Существует много интересных вариантов использования алгоритмов машинного обучения, которые позволяют получить любопытные аналитические сведения из данных телеметрии. Часто для этих алгоритмов в качестве входных данных требуется использовать строго структурированный набор данных. Необработанные данные журнала обычно не соответствуют требованиям с точки зрения структуры и размера. 

Для начала следует найти аномалии в частоте ошибок определенной службы вывода Bing. Таблица журналов содержит 65 млрд записей. Следующий базовый запрос фильтрует 250 000 ошибок, а затем создается временной ряд числа ошибок, для которого используется функция обнаружения аномалий [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Аномалии обнаруживаются службой Kusto и выделяются красными точками на диаграмме временных рядов.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Служба определила несколько временных контейнеров с подозрительными частотами ошибок. Воспользуйтесь Kusto для масштабирования до этого диапазона времени. Затем отправьте запрос, который выполняет статистическую обработку по столбцу `Message`. Попробуйте найти самые распространенные ошибки. 

Соответствующие части всей трассировки стека сообщения обрезаются, чтобы результаты лучше помещались на страницу. 

Как можно видеть, первые восемь ошибок успешно идентифицированы. Однако далее следует длинный ряд ошибок, так как сообщение об ошибке создано с использованием строки формата, содержащей меняющиеся данные.

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
|7125|Сбой ExecuteAlgorithmMethod для метода "RunCycleFromInterimData"...
|7125|Сбой вызова InferenceHostService. System.NullReferenceException. Ссылка на объект не указывает на экземпляр объекта...
|7124|Непредвиденная ошибка системы вывода. System.NullReferenceException. Ссылка на объект не указывает на экземпляр объекта... 
|5112|Непредвиденная ошибка системы вывода. System.NullReferenceException. Ссылка на объект не указывает на экземпляр объекта...
|174|Сбой вызова InferenceHostService. System.ServiceModel.CommunicationException. Произошла ошибка при записи в канал...
|10|Сбой ExecuteAlgorithmMethod для метода "RunCycleFromInterimData"...
|10|Ошибка системы вывода. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException...
|3|Сбой вызова InferenceHostService. System.ServiceModel.CommunicationObjectFaultedException...
|1|Ошибка системы вывода... SocialGraph.BOSS.OperationResponse...AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|Ошибка системы вывода... SocialGraph.BOSS.OperationResponse...AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF...

На этом этапе удобно использовать оператор `reduce`. Оператор определил 63 различные ошибки, произошедшие в одной и той же точке инструментирования трассировки в коде. `reduce` помогает сосредоточиться на дополнительных значимых трассировках ошибок в этом временном окне.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Счетчик|Шаблон
|---|---
|7125|Сбой ExecuteAlgorithmMethod для метода "RunCycleFromInterimData"...
|  7125|Сбой вызова InferenceHostService. System.NullReferenceException. Ссылка на объект не указывает на экземпляр объекта...
|  7124|Непредвиденная ошибка системы вывода. System.NullReferenceException. Ссылка на объект не указывает на экземпляр объекта...
|  5112|Непредвиденная ошибка системы вывода. System.NullReferenceException. Ссылка на объект не указывает на экземпляр объекта...
|  174|Сбой вызова InferenceHostService. System.ServiceModel.CommunicationException. Произошла ошибка при записи в канал...
|  63|Ошибка системы вывода. Microsoft.Bing.Platform.Inferences.\*. Запишите \* для записи в объект BOSS.\*. SocialGraph.BOSS.Reques...
|  10|Сбой ExecuteAlgorithmMethod для метода "RunCycleFromInterimData"...
|  10|Ошибка системы вывода. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException...
|  3|Сбой вызова InferenceHostService. System.ServiceModel.\*. Объект System.ServiceModel.Channels.\*+\* для \*\* является \*... в Syst...

Теперь у вас есть неплохое представление об основных ошибках, которые привели к обнаруженным аномалиям.

Чтобы понять последствия этих ошибок в примере системы, следует учесть следующие аспекты. 
* Таблица `Logs` содержит дополнительные многомерные данные, например `Component` и `Cluster`.
* Используя новый подключаемый модуль autocluster, можно получить аналитические сведения о компоненте и кластере с помощью простого запроса. 

Как видно из следующего примера, каждая из четырех основных ошибок характерна только для компонента. Кроме того, хотя три основных ошибки характерны только для кластера DB4, четвертая ошибка происходит во всех кластерах.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Счетчик |Процент (%)|Компонент|Кластер|Сообщение
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|ExecuteAlgorithmMethod для метода...
|7125|26.64|Неизвестный компонент|DB4|Сбой вызова InferenceHostService...
|7124|26.64|InferenceAlgorithmExecutor|DB4|Непредвиденная ошибка системы вывода...
|5112|19.11|InferenceAlgorithmExecutor|*|Непредвиденная ошибка системы вывода...

## <a name="map-values-from-one-set-to-another"></a>Сопоставление значений из одного набора с другим

Распространенный вариант использования запроса — статическое сопоставление значений. Статическое сопоставление позволяет улучшить представление результатов.

Например, в следующей таблице `DeviceModel` задает модель устройства. Модель устройства неудобно использовать для ссылки на имя устройства.  

|DeviceModel |Счетчик 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

 Удобнее использовать понятное имя.  

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

В следующих двух примерах показано, как вместо модели устройства использовать для идентификации устройства понятное имя.  

### <a name="map-by-using-a-dynamic-dictionary"></a>Сопоставление с помощью динамического словаря

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
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|

### <a name="map-by-using-a-static-table"></a>Сопоставление с помощью статической таблицы

Сопоставление можно также выполнить с помощью постоянной таблицы и оператора `join`.
 
1. Создайте таблицу сопоставлений только один раз.

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. Создайте таблицу содержимого устройства.

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5,1 |iPhone 5 
    |iPhone3,2 |iPhone 4 
    |iPhone7,2 |iPhone 6 
    |iPhone5,2 |iPhone5 

1. Создайте источник тестовой таблицы.

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. Присоедините таблицы и запустите проект.

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

Выходные данные будут выглядеть следующим образом.

|FriendlyName |Счетчик 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Создание и использование таблиц измерения времени выполнения запросов

Зачастую результаты запроса требуется объединить со специализированной таблицей измерения, которая не хранится в базе данных. Для этого можно определить выражение, результатом которого является таблица, ограниченная одним запросом. 

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

Ниже приведен немного более сложный пример.

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

Предположим, что у вас есть таблица, которая включает в себя следующие компоненты:
* столбец `ID`, определяющий сущность, с которой связана каждая строка, например идентификатор пользователя или идентификатор узла;
* столбец `timestamp`, предоставляющий ссылку на время для строки;
* Другие столбцы

Для запроса, возвращающего последние две записи для каждого значения столбца `ID` можно использовать [оператор top-nested](topnestedoperator.md). Под _последней_ следует понимать запись с _наибольшим значением `timestamp`_ .

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


Ниже приведено пошаговое описание предыдущего запроса (нумерация соответствует числам, указанным в комментариях к коду).

1. Оператор `datatable` используется для создания тестовых данных для демонстрационных целей. Обычно здесь используются фактические данные.
1. Эта строка фактически означает _возврат всех уникальных значений `id`_ . 
1. Поэтому она возвращает для двух первых записей с максимальными значениями:
     * столбец `timestamp`;
     * столбцы предыдущего уровня (здесь просто `id`);
     * столбец, указанный на этом уровне (здесь `timestamp`).
1. Эта строка добавляет значения столбца `bla` для каждой записи, возвращенной предыдущим уровнем. Если таблица содержит другие интересующие вас столбцы, эту строку можно повторить для каждого из них.
1. В последней строке используется [оператор project-away](projectawayoperator.md), удаляющий "лишние" столбцы, добавленные оператором `top-nested`.

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>Добавление в таблицу процентов от общего итога вычисления

Пользователю удобнее использовать табличное выражение с числовым столбцом, если оно дополнено значением, равным проценту от общей суммы.

Предположим, например, что запрос создает следующую таблицу:

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

Необходимо, чтобы таблица выглядела следующим образом:

|SomeSeries|SomeInt|Pct |
|----------|-------|----|
|Apple       |    100|33,3|
|Banana       |    200|66,6|

Чтобы изменить способ отображения таблицы, вычислите сумму (sum) столбца `SomeInt`, а затем разделите каждое значение этого столбца на общую сумму. Для произвольных результатов используйте [оператор as](asoperator.md).

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

## <a name="perform-aggregations-over-a-sliding-window"></a>Вычисление агрегатов для скользящего окна

В следующем примере показано, как суммировать столбцы с помощью скользящего окна. Для запроса используйте следующую таблицу, которая содержит цены на фрукты по меткам времени.

Вычислите минимальные, максимальные и суммарные затраты на каждый фрукт за день, используя скользящее окно в семь дней. Каждая запись в результирующем наборе агрегирует данные за предыдущие семь дней, а результаты содержат запись за день в период анализа.

Таблица фруктов

|Отметка времени|Фрукт|Цена|
|---|---|---|
|2018-09-24 21:00:00.0000000|Бананы|3|
|2018-09-25 20:00:00.0000000|Яблоки|9|
|2018-09-26 03:00:00.0000000|Бананы|4|
|2018-09-27 10:00:00.0000000|Сливы|8|
|2018-09-28 07:00:00.0000000|Бананы|6|
|2018-09-29 21:00:00.0000000|Бананы|8|
|2018-09-30 01:00:00.0000000|Сливы|2|
|2018-10-01 05:00:00.0000000|Бананы|0|
|2018-10-02 02:00:00.0000000|Бананы|0|
|2018-10-03 13:00:00.0000000|Сливы|4|
|2018-10-04 14:00:00.0000000|Яблоки|8|
|2018-10-05 05:00:00.0000000|Бананы|2|
|2018-10-06 08:00:00.0000000|Сливы|8|
|2018-10-07 12:00:00.0000000|Бананы|0|

Ниже приведен запрос агрегирования по скользящему окну. Объяснение приведено после результата запроса.

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
|2018-10-01 00:00:00.0000000|Сливы|2|8|10|
|2018-10-02 00:00:00.0000000|Бананы|0|8|18|
|2018-10-02 00:00:00.0000000|Сливы|2|8|10|
|2018-10-03 00:00:00.0000000|Сливы|2|8|14|
|2018-10-03 00:00:00.0000000|Бананы|0|8|14|
|2018-10-04 00:00:00.0000000|Бананы|0|8|14|
|2018-10-04 00:00:00.0000000|Сливы|2|4|6|
|2018-10-04 00:00:00.0000000|Яблоки|8|8|8|
|2018-10-05 00:00:00.0000000|Бананы|0|8|10|
|2018-10-05 00:00:00.0000000|Сливы|2|4|6|
|2018-10-05 00:00:00.0000000|Яблоки|8|8|8|
|2018-10-06 00:00:00.0000000|Сливы|2|8|14|
|2018-10-06 00:00:00.0000000|Бананы|0|2|2|
|2018-10-06 00:00:00.0000000|Яблоки|8|8|8|
|2018-10-07 00:00:00.0000000|Бананы|0|2|2|
|2018-10-07 00:00:00.0000000|Сливы|4|8|12|
|2018-10-07 00:00:00.0000000|Яблоки|8|8|8|

Запрос "растягивает" (дублирует) каждую запись во входной таблице на семь дней после ее фактического добавления. По сути, каждая запись отображается семь раз. В результате ежедневное агрегирование включает все записи за предыдущие семь дней.


Ниже приведено пошаговое описание предыдущего запроса. 

1. Для каждой записи используется интервал в один день (относительно `_start`). 
1. Определите конец диапазона на запись: `_bin + 7d`. Если значение выходит за пределы диапазона от `_start` до `_end`, требуется корректировка. 
1. Для каждой записи создайте массив из семи дней (меток времени), начиная с дня текущей записи. 
1. Примените к этому массиву оператор `mv-expand`, чтобы продублировать каждую запись семь раз отдельно для каждого дня. 
1. Выполните статистическую функцию для каждого дня. Вследствие выполнения шага 4 на этом шаге фактически суммируются данные за _последние семь дней_. 
1. Данные за первые семь дней неполные, так как для них отсутствует семидневный период ретроспективного обзора. Эти первые семь дней исключаются из конечного результата. В этом примере они задействованы только в агрегировании для 2018-10-01.

## <a name="find-the-preceding-event"></a>Поиск предыдущего события

В следующем примере показано, как найти предыдущее событие между двумя наборами данных.  

У вас есть два набора данных: A и B. Для каждой записи в наборе данных B необходимо найти предыдущее событие в наборе данных A (то есть запись `arg_max` в наборе A, которая при этом является _более старой_, чем в наборе B).

Ниже приведены примеры наборов данных. 

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

|Отметка времени|ID|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|з|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|да|Ay1|
|2019-01-01 00:00:05.0000000|да|Ay2|

</br>

|Отметка времени|ID|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|да|B|
|2019-01-01 00:02:00.0000000|з|B|

Ожидаемые выходные данные: 

|ID|Отметка времени|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|да|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|з|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Для этой проблемы мы рекомендуем два разных способа. Вы можете протестировать оба для конкретного набора данных, чтобы найти наиболее подходящий для своего сценария.

> [!NOTE] 
> Каждый способ может работать по-разному для разных наборов данных.

### <a name="approach-1"></a>Способ 1

Этот подход предусматривает сериализацию обоих наборов данных по идентификатору и метке времени. Затем он группирует все события в наборе данных B со всеми предшествующими им событиями в наборе данных A. Наконец, он выбирает `arg_max` из всех событий в наборе данных A в группе.

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

### <a name="approach-2"></a>Способ 2.

Для решения задачи этим способом требуется максимальный период ретроспективного обзора. Данный способ предусматривает определения того, насколько запись в наборе данных A может быть _старее_ записи в наборе данных B. Затем два набора данных соединяются на основе идентификатора и этого полученного периода ретроспективного обзора.

Оператор `join` создает все возможные кандидаты: все записи набора данных A, которые старее записей в наборе данных B и относятся к периоду ретроспективного обзора. Затем ближайшая к набору данных B запись фильтруется по `arg_min (TimestampB - TimestampA)`. Чем короче период ретроспективного обзора, тем лучше результаты запроса.

В следующем примере для периода ретроспективного обзора задано значение `1m`. Запись с идентификатором `z` не имеет соответствующего события `A`, так как ее событие `A` старее на две минуты.

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

|ID|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|да|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|з|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>Дальнейшие действия

- Выполните пошаговые инструкции в [учебнике по языку запросов Kusto](tutorial.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

В этой статье приведены общие требования к запросам в Azure Monitor и показано, как обеспечить их соблюдение с помощью языка запросов Kusto.

## <a name="string-operations"></a>Операции со строками

В следующих разделах приведены примеры работы со строками при использовании языка запросов Kusto.

### <a name="strings-and-how-to-escape-them"></a>Строки и способ применения к ним escape-последовательностей

Строковые значения заключаются в одинарные или двойные кавычки. Добавьте обратную косую черту (\\) слева от символа, чтобы преобразовать его в escape-последовательность: `\t` для табуляции, `\n` для новой строки и `\"` для символа одинарной кавычки.

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
`=~`           |Равно                              |Нет            |`"abc" =~ "ABC"`
`!~`           |Не равно                          |Нет            |`"aBc" !~ "xyz"`
`has`          |Значение справа — это весь термин в значении слева. |Нет|`"North America" has "america"`
`!has`         |Значение справа — это не полный термин в значении слева.       |Нет            |`"North America" !has "amer"` 
`has_cs`       |Значение справа — это весь термин в значении слева. |Да|`"North America" has_cs "America"`
`!has_cs`      |Значение справа — это не полный термин в значении слева.       |Да            |`"North America" !has_cs "amer"` 
`hasprefix`    |Значение справа — это префикс термина в значении слева.         |Нет            |`"North America" hasprefix "ame"`
`!hasprefix`   |Значение справа — это не префикс термина в значении слева.     |Нет            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Значение справа — это префикс термина в значении слева.         |Да            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Значение справа — это не префикс термина в значении слева.     |Да            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Значение справа — это суффикс термина в значении слева.         |Нет            |`"North America" hassuffix "ica"`
`!hassuffix`   |Значение справа — это не суффикс термина в значении слева.     |Нет            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Значение справа — это суффикс термина в значении слева.         |Да            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Значение справа — это не суффикс термина в значении слева.     |Да            |`"North America" !hassuffix_cs "icA"`
`contains`     |Значение справа является вложенной последовательностью значения слева.  |Нет            |`"FabriKam" contains "BRik"`
`!contains`    |Значение справа не встречается в значении слева.           |Нет            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Значение справа является вложенной последовательностью значения слева.  |Да           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Значение справа не встречается в значении слева.           |Да           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Значение справа является начальной вложенной последовательностью значения слева.|Нет            |`"Fabrikam" startswith "fab"`
`!startswith`  |Значение справа не является начальной вложенной последовательностью значения слева.|Нет        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Значение справа является начальной вложенной последовательностью значения слева.|Да            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Значение справа не является начальной вложенной последовательностью значения слева.|Да        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Значение справа является конечной вложенной последовательностью значения слева.|Нет             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Значение справа не является конечной вложенной последовательностью значения слева.|Нет         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Значение справа является конечной вложенной последовательностью значения слева.|Да             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Значение справа не является конечной вложенной последовательностью значения слева.|Да         |`"Fabrikam" !endswith "brik"`
`matches regex`|Значение слева содержит соответствие для значения справа|Да           |`"Fabrikam" matches regex "b.*k"`
`in`           |Соответствует одному из элементов       |Да           |`"abc" in ("123", "345", "abc")`
`!in`          |Не соответствует одному из элементов   |Да           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

Подсчитывает вхождения подстроки в строку. Может сопоставлять обычные строки или использовать регулярное выражение. Совпадения для обычных текстовых строк могут перекрываться, а для регулярных — нет.

```
countof(text, search [, kind])
```

- `text`. Строка входных данных 
- `search`. Обычная строка или регулярное выражение для поиска соответствия в text.
- `kind`: _normal_ | _regex_ (по умолчанию: normal).

Возвращает количество совпадений для строки поиска в контейнере. Совпадения для обычных текстовых строк могут перекрываться, а для регулярных — нет.

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

определяет соответствие регулярного выражения из определенной строки. При необходимости извлеченная подстрока может быть приведена к указанному типу.

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`. Регулярное выражение.
- `captureGroup`. Положительная целочисленная константа, указывающая извлекаемую группу записи. Используйте 0 для полного совпадения, 1 для значения, соответствующего первой круглой скобке \(\) в регулярном выражении, и 2 или более для последующих круглых скобок.
- `text`. Искомая строка.
- `typeLiteral`. Необязательный литерал типа (например, `typeof(long)`). Если указан, то извлеченная подстрока преобразуется в этот тип.

Возвращает подстроку, которая сравнивалась с указанной группой `captureGroup` в группе записи и при необходимости была приведена к типу `typeLiteral`. Если соответствия нет или не удается выполнить преобразование типа, возвращается NULL.

Следующий пример извлекает последний октет `ComputerIP` из записи пакета пульса.

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Следующий пример извлекает последний октет, приводит его к типу (числа) *real*, а затем вычисляет следующее значение IP-адреса.

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

В следующем примере в строке `Trace` выполняется поиск определения `Duration`. Соответствие приводится к типу `real` и умножается на константу времени (1 с), которая приводит `Duration` к типу `timespan`.

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*isempty*, *isnotempty*, *notempty*

- Функция `isempty` возвращает значение `true`, если аргумент является пустой строкой или имеет значение null (см. описание для `isnull`).
- Функция `isnotempty` возвращает значение `true`, если аргумент не является пустой строкой и не имеет значение null (см. описание для `isnotnull`). Псевдоним: `notempty`.


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


### <a name="parseurl"></a>*parseurl*

Разделяет URL-адрес на части (например, протокол, узел, порт и т. д.), а затем возвращает объект словаря, содержащий элементы в виде строк.

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

- `regex`. Регулярное выражение для сопоставления. Оно может содержать группы записи в (круглых скобках) \(\).
- `rewrite`. Регулярное выражение, результат которого заменит все соответствия, найденные при сопоставлении с помощью регулярного выражения. Используйте \0 для указания полного соответствия, \1 для первой группы записи, \2 и так далее для следующих групп записи.
- `input_text`. Строка входных данных для поиска.

Возвращает текст после замены всех вхождений регулярного выражения результатами вычислений rewrite. Совпадения не перекрываются.

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

Разделяет определенную строку в соответствии с указанным разделителем и возвращает массив результирующих подстрок.

```
split(source, delimiter [, requestedIndex])
```

- `source`. Строка для разделения в соответствии с указанным разделителем.
- `delimiter`. Разделитель, который будет использоваться для разделения исходной строки.
- `requestedIndex`. Необязательный индекс, начинающийся с нуля. Если указан, то возвращаемый массив строк содержит только этот элемент (если существует).


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

Извлекает подстроку из определенной исходной строки, начиная с указанного индекса. При необходимости можно указать длину запрашиваемой подстроки.

```
substring(source, startingIndex [, length])
```

- `source`. Исходная строка, из которой будет извлечена подстрока.
- `startingIndex`. Отсчитываемая от нуля позиция первого знака запрашиваемой подстроки.
- `length`. Необязательный параметр, который указывает запрашиваемую длину возвращаемой подстроки.

#### <a name="example"></a>Пример

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*tolower*, *toupper*

Преобразует все символы указанной строки в символы нижнего или верхнего регистра.

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

В следующих разделах приведены примеры работы со значениями даты и времени при использовании языка запросов Kusto.

### <a name="date-time-basics"></a>Основные сведения о дате и времени

Язык запросов Kusto содержит два основных типа данных, связанных с датами и временем: `datetime` и `timespan`. Все даты задаются в формате UTC. Хотя поддерживаются несколько форматов даты и времени, рекомендуется использовать формат ISO 8601. 

Интервалы времени выражаются как десятичное число, за которым следует единица времени:

|Сокращение   | Единица времени    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | миллисекунда  |
|микросекунда | микросекунда  |
|галочка        | наносекунда   |

Значения даты и времени можно создать путем приведения строки с помощью оператора `todatetime`. Например, чтобы просмотреть пульс виртуальной машины, отправленный в определенный период времени, можно использовать оператор `between`, с помощью которого удобнее указывать диапазон времени.

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Другим распространенным сценарием является сравнение значения даты и времени с настоящим временем. Например, чтобы просмотреть все пакеты пульса за последние две минуты, можно использовать оператор `now` вместе с промежутком времени в две минуты:

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Для этой функции также доступно сокращение:

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Кратчайший и самый удобочитаемый метод — использовать оператор `ago`.

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Предположим, что вместо времени начала и окончания вы знаете время начала и длительность. Вы можете переписать запрос.

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>Преобразование единиц времени

Иногда необходимо выразить значение даты и времени или интервала времени в единицах времени, отличных от значения по умолчанию. Например, если вы просматриваете события, вызвавшие ошибки за последние 30 минут, и вам нужен вычисляемый столбец, который показывает, как давно произошло событие, можно использовать приведенный ниже запрос.

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Столбец `timeAgo` содержит такие значения, как `00:09:31.5118992`, которые форматируются следующим образом: чч:мм:сс.fffffff. Если вы хотите форматировать эти значения как значение `number`, равное числу минут с времени начала, поделите их на `1m`.

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>Агрегирование и группирование по интервалам времени

Другой очень распространенный сценарий — получение статистики за конкретный период времени, измеряемый в определенных единицах времени. В этом сценарии в предложении `summarize` можно использовать оператор `bin`.

Чтобы получить количество событий, которые происходили каждые пять минут в течение последнего получаса, используйте следующий запрос:

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

Другой способ создания контейнеров результатов — использовать такие функции, как `startofday`.

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

Поскольку все значения даты и времени выражены в формате UTC, возможно будет удобнее, если преобразовать эти значения в формат местного часового пояса. Например, используйте это вычисление для преобразования времени UTC в формат PST (тихоокеанское время):

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Агрегации

В следующих разделах приведены примеры агрегации результатов запроса при использовании языка запросов Kusto.

### <a name="count"></a>*count*

Выполните подсчет количества строк в результирующем наборе после применения фильтров. Следующий пример возвращает общее количество строк в таблице `Perf` за последние 30 минут. Результат возвращается в столбец под именем `count_`, если этому столбцу присвоено конкретное имя.


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

Чтобы просмотреть тенденцию по времени, можно использовать визуализацию временной диаграммы.

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

В результате этого примера показана линия тенденции количества записей `Perf` с пятиминутными интервалами.


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="Снимок экрана, на котором показан график линии тенденции количества записей Perf за пятиминутные интервалы.":::

### <a name="dcount-dcountif"></a>*dcount*, *dcountif*

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

### <a name="evaluate-subgroups"></a>Вычисление подгрупп

Чтобы выполнить подсчет или другие агрегирования с подгруппами в данных, используйте ключевое слово `by`. Например, чтобы подсчитать количество отдельных компьютеров Linux, которые отправляли пакеты пульса в каждой стране или регионе, используйте следующий запрос.

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


Чтобы проанализировать меньшие подгруппы данных, добавьте имена столбцов в раздел `by`. Например, может потребоваться подсчитать количество отдельных компьютеров из каждой страны или региона для каждого типа операционной системы (`OSType`).

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

Вы также можете указать различные процентили, чтобы получить агрегированный результат для каждого из них.

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Результаты могут показывать, что некоторые ЦП компьютера имеют сходные значения медианы. При этом некоторые компьютеры постоянно остаются вблизи медианы, а значения для ЦП других могут быть значительно ниже или выше. Высокие и низкие значения означают, что компьютеры испытывают пиковые нагрузки.

### <a name="variance"></a>Variance

Чтобы напрямую рассчитать дисперсию значения, используйте стандартное отклонение и методы дисперсии:

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Хороший способ проанализировать стабильность загрузки ЦП — объединить значение `stdev` с результатом вычисления медианы.

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>Создание списков и наборов

Вы можете использовать `makelist`, чтобы свести данные по порядку значений в определенном столбце. Например, вы можете изучить наиболее распространенные упорядоченные события на компьютерах. Вы можете сводить данные по порядку значений `EventID` на каждом компьютере. 

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

`makelist` формирует список в порядке, в котором были переданы данные. Чтобы отсортировать события от старых к новым, используйте `asc` вместо `order` в инструкции `desc`. 

Иногда полезно создать список, содержащий только отличающиеся значения. Этот список называется _набор_, и его можно создать с помощью команды `makeset`.

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

Как и `makelist`, `makeset` также работает с упорядоченными данными. Команда `makeset` создает массивы по порядку переданных в нее строк.

### <a name="expand-lists"></a>Расширение списков

Операция, обратная к `makelist` или `makeset`, — это `mv-expand`. Команда `mv-expand` расширяет список значений для разделения строк. Ее можно развернуть в любом количестве динамических столбцов, в том числе в столбцах JSON и массива. Например, вы можете просканировать таблицу `Heartbeat` для решений, отправляющих данные с компьютеров, которые отправили пакет пульса за последний час.

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

Используйте `mv-expand`, чтобы показать каждое значение в отдельной строке, а не в виде списка значений, разделенных запятыми.

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


Для группирования элементов можно использовать `makelist`. В выходных данных отображается список компьютеров для каждого решения.

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

### <a name="missing-bins"></a>Отсутствующие интервалы

`mv-expand` полезно применять для заполнения значений по умолчанию для отсутствующих интервалов. Предположим, что вам нужно определить время доступности определенного компьютера с помощью анализа пульса. Также вы хотите просмотреть источник пульса, который находится в столбце `Category`. Обычно используется базовая инструкция `summarize`.

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

Однако, в этих выходных данных контейнер, связанный с "2017-06-06T19:00:00Z", отсутствует, так как отсутствуют данные пульса для этого времени. Используйте функцию `make-series`, чтобы присвоить значение по умолчанию пустым контейнерам. Для каждой категории создается строка. Выходные данные содержат два дополнительных столбца массива: один для значений и один для сопоставления контейнеров по времени.

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

Выходные данные будут выглядеть следующим образом.

| Категория | count_ | TimeGenerated |
|---|---|---|
| Direct Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Третий элемент массива *count_* , как и ожидалось, равен 0. В массиве _TimeGenerated_ имеется совпадение по метке времени "2017-06-06T19:00:00.0000000Z". Однако этот формат массива трудно прочитать. Используйте `mv-expand`, чтобы развернуть массивы и создать выходные данные того же формата, что и сгенерированные `summarize`:

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



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Сведите результаты до набора элементов: *let*, *makeset*, *toscalar*, *in*.

Распространенным сценарием является выбор имен конкретных сущностей на основе набора критериев, а затем фильтрация другого набора данных до этого набора сущностей. Например, вы можете найти компьютеры, у которых отсутствуют обновления, и определить IP-адреса, для которых вызываются эти компьютеры.

Ниже приведен пример:

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

Соединения можно использовать для анализа данных из нескольких таблиц в одном запросе. Соединение объединяет строки двух наборов данных, сопоставляя значения из указанных столбцов.

Ниже приведен пример:

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

В этом примере первый набор данных фильтрует все события входа. Он объединяется со вторым набором данных, который фильтрует все события выхода. Проецируемые столбцы: `Computer`, `Account`, `TargetLogonId` и `TimeGenerated`. Наборы данных сопоставляются по общему столбцу `TargetLogonId`. Результат — одна запись на сопоставление, в которой есть и время входа и время выхода.

Если оба набора данных имеют столбцы с одинаковыми именами, то столбцам в правом наборе данных присваивается номер индекса. В этом примере в результатах будет отображаться `TargetLogonId` со значениями из таблицы слева и `TargetLogonId1` со значениями из таблицы справа. В этом случае второй столбец `TargetLogonId1` был удален с помощью оператора `project-away`.

> [!NOTE]
> Для повышения производительности оставьте только соответствующие столбцы объединенных наборов данных, используя оператор `project`.


Используйте следующий синтаксис, чтобы объединить два набора данных, в которых присоединенный ключ имеет разные имена в каждой из таблиц.

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Таблицы подстановки

Распространенное применение соединений — использование `datatable` для сопоставления статических значений. Использование `datatable` позволяет улучшить представление результатов. Например, можно добавить к данным событий безопасности имя события для каждого идентификатора.

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
| Закрыт дескриптор для объекта | 290 995 |
| Запрошен дескриптор для объекта | 154 157 |
| Предпринята попытка дублировать дескриптор для объекта | 144 305 |
| Предпринята попытка доступа к объекту | 123 669 |
| Операция шифрования | 153 495 |
| Операция с файлом ключа | 153 496 |

## <a name="json-and-data-structures"></a>JSON и структуры данных

Вложенные объекты — это объекты, которые содержат другие объекты в виде массива или карты пар "ключ-значение". Объекты представлены как строки JSON. В этом разделе описывается, как с помощью JSON извлечь данные и проанализировать вложенные объекты.

### <a name="work-with-json-strings"></a>Работа со строками JSON

Используйте `extractjson`, чтобы получить доступ к определенному элементу JSON с известным путем. Для этой функции требуется выражение пути, в котором используются следующие соглашения:

- Использование _$_ для обращения к корневой папке.
- Использование скобок или точечной нотации для обращения к индексам и элементам, как показано в следующих примерах.


Использование скобок для индексов и точек для разделения элементов:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Приведенный ниже пример аналогичен, но в нем используется только нотация квадратных скобок.

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Если элемент только один, можно использовать только точечную нотацию:

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

Чтобы получить доступ к нескольким элементам в структуре JSON, проще всего открыть их как динамический объект. Используйте `parsejson` для приведения текстовых данных к динамическому объекту. После преобразования JSON в динамический тип можно использовать для анализа данных дополнительные функции.

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

Используйте `mv-expand`, чтобы разбить свойства объекта на отдельные строки.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="Снимок экрана с изображением hosts_0 со значениями для location, status и rate.":::

### <a name="buildschema"></a>*buildschema*

Используйте `buildschema` для получения схемы, которая учитывает все значения объекта:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Результат — схема в формате JSON.

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

Эта схема описывает имена полей объекта и соответствующие им типы данных. 

Вложенные объекты могут иметь разные схемы, такие как в следующем примере.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Диаграммы

В следующих разделах приведены примеры работы с диаграммами при использовании языка запросов Kusto.

### <a name="chart-the-results"></a>Построение диаграммы результатов

Для начала определите, сколько компьютеров работало под управлением каждой операционной системы за последний час.

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

По умолчанию результаты отображаются в виде таблицы.

:::image type="content" source="images/samples/query-results-table.png" alt-text="Снимок экрана, на котором показаны результаты запроса в таблице.":::

Чтобы получить более удобное визуальное представление, выберите **Диаграмма**, а затем — **Круговая**.

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="Снимок экрана, на котором показаны результаты запроса в виде круговой диаграммы.":::

### <a name="timecharts"></a>Временные диаграммы

Показать среднее количество 50- и 95-процентильной загруженности процессора на интервалах за час. 

Следующий запрос позволяет создать несколько рядов. В результатах можно выбрать, какие ряды будут отображаться на временной диаграмме.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Выберите вариант отображения диаграммы **Линейная**.

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="Снимок экрана, на котором показан график с несколькими рядами.":::

#### <a name="reference-line"></a>Справочные данные

Опорная линия поможет легко определить, превышает ли метрика указанный порог. Чтобы добавить линию на диаграмму, дополните набор данных столбцом констант.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="Снимок экрана, на котором показан график с несколькими рядами и опорной линией для порогового значения.":::


### <a name="multiple-dimensions"></a>Несколько измерений

Несколько выражений в значении `by` из `summarize` создают в результатах несколько строк. Для каждого сочетания значений создается одна строка.

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

При просмотре результатов в виде диаграммы используется первый столбец из предложения `by`. В следующем примере показана гистограмма с накоплением, созданная с использованием значения `EventID`. Измерения должны относиться к типу `string`. В этом примере значение `EventID` приводится к типу `string`.

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="Снимок экрана, на котором показана линейчатая диаграмма на основе столбца EventID.":::

Нажав стрелку раскрывающегося меню рядом с именем столбца, можно переключаться между столбцами.

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="Снимок экрана, на котором показана линейчатая диаграмма на основе столбца AccountType с видимым селектором столбцов.":::

## <a name="smart-analytics"></a>Смарт-аналитика

Этот раздел содержит примеры использования функций смарт-аналитики в Azure Log Analytics для анализа активности пользователей. Вы можете использовать эти примеры для анализа приложений, отслеживаемых Azure Application Insights, или применять общие сведения из этих запросов для выполнения аналогичного анализа других данных. 

### <a name="cohorts-analytics"></a>Аналитика когорт

Анализ когорт позволяет отслеживать действия конкретных групп пользователей, известных как _когорты_. Во время анализа когорт предпринимается попытка вычисления привлекательности службы путем измерения количества возвращающихся пользователей. Пользователи группируются по времени первого использования службы. При анализе когорт нам необходимо найти снижение активности за первые отслеживаемые периоды. В названии каждой когорты обозначается неделя, в которую ее участники были замечены впервые.

В следующем примере анализируется количество действий, которые пользователи выполняют в течение пяти недель после первого использования службы.

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

:::image type="content" source="images/samples/cohorts-table.png" alt-text="Снимок экрана, на котором показана таблица когорт на основе действий.":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Обработка данных ежемесячных активных пользователей и удержание пользователей

В следующем примере используется анализ временных рядов с функцией [series_fir](/azure/kusto/query/series-firfunction). Для вычислений по скользящему окну можно использовать функцию `series_fir`. Пример приложения, для которого выполняется мониторинг, — это интернет-магазин, отслеживающий активность пользователей через пользовательские события. Запрос отслеживает два типа действий пользователей: `AddToCart` и `Checkout`. Он определяет активного пользователя как пользователя, который оформил заказ по крайней мере один раз в определенный день.

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

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="Снимок экрана, на котором показана диаграмма, демонстрирующая число регулярно активных пользователей по дням за месяц.":::

В следующем примере предыдущий запрос преобразуется в пригодную для повторного использования функцию. Затем в примере используется запрос для вычисления удержания регулярных пользователей. Активный пользователь в этом запросе определяется как пользователь, который оформил заказ по крайней мере один раз в определенный день.

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

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="Снимок экрана, на котором показана диаграмма удержания пользователей с течением времени.":::

### <a name="regression-analysis"></a>Регрессионный анализ

В этом примере показано, как создать автоматическое средство обнаружения сбоев в работе службы исключительно на основе журналов трассировки приложения. Средство обнаружения ищет аномальное внезапное увеличение относительного количества ошибок и выполняет трассировку предупреждений в приложении.

Для оценки состояния службы на основе данных журналов трассировки используются два метода:

- [make-series](/azure/kusto/query/make-seriesoperator) — для преобразования полуструктурированных журналов текстовых сообщений в метрику, которая представляет собой соотношение между положительными и отрицательными линиями трассировки.
- [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) и [series_fit_line](/azure/kusto/query/series-fit-linefunction) — для расширенного обнаружения резкого перехода с использованием анализа временных рядов с помощью двухстрочной линейной регрессии.

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

- Выполните пошаговые инструкции в [учебнике по языку запросов Kusto](tutorial.md?pivots=azuremonitor).


::: zone-end

