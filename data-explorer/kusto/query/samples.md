---
title: Образцы - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны образцы в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 746017d41b5f1a13ce73f2c27df9cac5b5982ff0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663587"
---
# <a name="samples"></a>Примеры

Ниже приведены несколько общих потребностей запроса и как язык запроса Kusto может быть использован для их удовлетворения.

## <a name="display-a-column-chart"></a>Отобразить диаграмму столбца

Проект два или более столбцов и использовать их в качестве x и y оси диаграммы:

```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* Первая колонна образует ось х. Это может быть числовый, дата, или строки. 
* `where`Используйте `summarize` `top` и ограничьте объем отображаемых данных.
* Сортируйте результаты таким образом, чтобы определить порядок оси x.

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

Используйте [`let`](./letstatement.md) для того, чтобы назвать проекцию таблицы, которая будет урезана как можно дальше, перед тем, как войти в соединение.
[`Project`](./projectoperator.md)используется для изменения имен меток времени таким образом, чтобы в результате могли отображаться как время начала, так и время остановки. Также выбираются другие столбцы, которые мы бы хотели видеть в результатах. [`join`](./joinoperator.md)соответствует начальному и стоп-записи для одного и того же действия, создавая строку для каждого действия. Наконец, `project` снова добавляет столбец для отображения длительности действия.


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

Затем мы группироваться по времени начала и IP, чтобы получить группу для каждой сессии. Мы должны `bin` предоставить функцию для параметра StartTime: если мы этого не сделаем, Kusto будет автоматически использовать 1-часовые бункеры, которые будут соответствовать некоторым время начала с неправильным временем остановки.

`arg_min`выбирает строку с наименьшей продолжительностью в `*` каждой группе, и параметр проходит через все другие столбцы, хотя он префиксирует "min_" к каждому имени столбца. 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

Затем мы можем добавить некоторый код для подсчета длительности в бункерах удобного размера. У нас есть небольшое предпочтение диаграммы `1s` бара, поэтому мы делим путем конвертировать временные промежутки в числа. 


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

Но вместо того, чтобы держать эти массивы, мы будем расширять их с помощью [mv-расширения:](./mvexpandoperator.md)

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

* Нам нужно todate time(), потому что [mv-expand](./mvexpandoperator.md) дает столбец динамического типа.
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

## <a name="introduce-null-bins-into-summarize"></a>Ввести нулевые ячейки в обобщение

Когда `summarize` оператор применяется над ключом группы, состоящим из `datetime` столбца, один обычно "закрепивает" эти значения в ячейки с фиксированной шириной. Например:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Эта операция создает таблицу с одним рядом `T` на группу строк, которые попадают в каждый ящик по пять минут. Чего он не делает, так это добавляет "null bins" - `StartTime` `StopTime` строки для значений ячейки времени между и для которых нет соответствующего ряда в `T`. 

Часто, желательно, чтобы "площадку" таблицу с этими бункерами. Вот один из способов сделать это:

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

Вот пошагзате объяснение запроса выше: 

1. Использование `union` оператора позволяет добавлять дополнительные строки в таблицу. Эти строки производятся выражением `union`.
2. Использование `range` оператора для создания таблицы с одним рядом/колонкой.
   Таблица не используется ни для `mv-expand` чего, кроме как для работы.
3. Использование `mv-expand` оператора над `range` функцией для создания столько строк, сколько `StartTime` 5-минутных ячеек между и `EndTime`.
4. Все с `Count` `0`.
5. Наконец, мы `summarize` используем оператора для группы вместе бункеров из исходного (слева, или внешнего) аргумента `union` и бункеров от внутреннего аргумента к нему (а именно, строки нулевой ячейки). Это гарантирует, что выход имеет одну строку в ячейке, значение которой равно нулю или исходное количество.  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>Получите больше от ваших данных в Kusto с помощью машинного обучения 

Есть много интересных случаев использования для использования алгоритмов машинного обучения и получить интересные идеи из телеметрических данных. Хотя часто эти алгоритмы требуют очень структурированного набора данных в качестве ввода, необработанные данные журнала, как правило, не соответствуют требуемой структуре и размеру. 

Наше путешествие начинается с поиска аномалий в частоте ошибок конкретной службы Bing Inferences. Таблица журналов имеет 65B записей, а простой запрос ниже фильтрует 250K ошибки, и создает временные ряды данных ошибок кол, который использует функцию обнаружения аномалий [series_decompose_anomalies.](series-decompose-anomaliesfunction.md) Аномалии обнаруживаются службой Kusto и высвечиваются в виде красных точек на графике временных рядов.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Служба идентифицировала несколько ведер времени с подозрительной частотой ошибок. Я использую Kusto для увеличения этого таймфрейма, запуская запрос, который агрегируется в столбце 'Message', пытаясь найти верхние ошибки. Я обрезал соответствующие части из всего стека след сообщения, чтобы лучше вписаться в страницу. Вы можете видеть, что я имел хороший успех с верхней восемь ошибок, но затем достиг длинный хвост ошибок, так как сообщение об ошибке был создан строки формата, который содержал изменения данных. 

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
|7125|ВыполнитьАлгоритмМетод для метода 'RunCycleFromInterimData' не удалось ...
|7125|Вызов InferenceHostService не удалось. System.NullReferenceException: Ссылка на объект, не установленная на экземпляр объекта...
|7124|Неожиданная ошибка системы выводов. System.NullReferenceException: Ссылка на объект, не установленная на экземпляр объекта... 
|5112|Неожиданная ошибка системы выводов. System.NullReferenceException: Ссылка на объект, не установленная на экземпляр объекта.
|174|InferenceHostService вызова не удалось..System.ServiceModel.CommunicationException: Была ошибка написания на трубу:...
|10|ВыполнитьАлгоритмМетод для метода 'RunCycleFromInterimData' не удалось ...
|10|Ошибка системы выводов. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|3|Вызов InferenceHostService не удалось..System.ServiceModel.CommunicationObjectFaultedИсключение:...
|1|Ошибка системы выводов... SocialGraph.BOSS.OperationResponse... AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|Ошибка системы выводов... SocialGraph.BOSS.OperationResponse... AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF...

Вот где `reduce` оператор приходит на помощь. Оператор `reduce` определил 63 различных ошибок, возникшых одной и той же точкой приборов в коде, и помог мне сосредоточиться на дополнительном значимом следе ошибки в этом временном окне.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Count|Модель
|---|---
|7125|ВыполнитьАлгоритмМетод для метода 'RunCycleFromInterimData' не удалось ...
|  7125|Вызов InferenceHostService не удалось. System.NullReferenceException: Ссылка на объект, не установленная на экземпляр объекта...
|  7124|Неожиданная ошибка системы выводов. System.NullReferenceException: Ссылка на объект, не установленная на экземпляр объекта...
|  5112|Неожиданная ошибка системы выводов. System.NullReferenceException: Ссылка на объект, не установленная на экземпляр объекта.
|  174|InferenceHostService вызова не удалось..System.ServiceModel.CommunicationException: Была ошибка написания на трубу:...
|  63|Ошибка системы выводов. Microsoft.Bing.Platform.Выводы. \*: \* Напишите, чтобы написать объекту BOSS. \*: SocialGraph.BOSS.Reques...
|  10|ВыполнитьАлгоритмМетод для метода 'RunCycleFromInterimData' не удалось ...
|  10|Ошибка системы выводов. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|  3|Вызов InferenceHostService не удалось. System.ServiceModel. \*: Объект, System.ServiceModel.Channels. \* \* \*, \* для является ... + \*   в Сист...

Теперь, когда у меня есть хорошее представление о лучших ошибках, которые способствовали обнаруженным аномалиям, я хочу понять влияние этих ошибок в моей системе. Таблица «Входы» содержит дополнительные объемные данные, такие как «Компонент», «Кластер» и т.д.... Новый плагин "автокластера" может помочь мне получить это понимание с помощью простого запроса. В приведенном ниже примере я могу ясно видеть, что каждая из четырех основных ошибок специфична для компонента, и, хотя три ошибки в топ-3 специфичны для кластера DB4, четвертая ошибка происходит во всех кластерах.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |Процент (%)|Компонент|Кластер|Сообщение
|---|---|---|---|---
|7125|26.64|ВыводХозяинСервис|DB4|ExecuteAlgorithmMethod для метода ....
|7125|26.64|Неизвестный компонент|DB4|Вызов InferenceHostService не удалось ....
|7124|26.64|ВыводАлгоритмЭксекутор|DB4|Неожиданная ошибка системы выводов...
|5112|19.11|ВыводАлгоритмЭксекутор|*|Неожиданная ошибка системы выводов... 

## <a name="mapping-values-from-one-set-to-another"></a>Отображение значений от одного набора к другому

Общее использование случае использует статическое отображение значений, которые могут помочь в принятии результатов в более презентаебным способом.  
Например, рассмотрите возможность наличия следующей таблицы. DeviceModel определяет модель устройства, которая не очень удобная форма ссылки на название устройства.  


|DeviceModel |Count 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

  
Лучшее представление может быть:  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Эти два подхода, приведенные ниже, свидетельствуют о том, как этого можно достичь.  

### <a name="mapping-using-dynamic-dictionary"></a>Картирование с помощью динамического словаря

Ниже приведенный подход показывает, как можно достичь картирования с помощью динамического словаря и динамических аксессуаров.

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

|FriendlyName|Count|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|



### <a name="mapping-using-static-table"></a>Картирование с помощью статического стола

Приведенный ниже подход показывает, как можно достичь отображения с помощью постоянной таблицы и присоединения оператора.
 
Создайте таблицу отображения (только один раз):

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Содержимое устройств сейчас:

|DeviceModel |FriendlyName 
|---|---
|iPhone5,1 |iPhone 5 
|iPhone3,2 |iPhone 4 
|iPhone7,2 |iPhone 6 
|iPhone5,2 |iPhone5 


Тот же трюк для создания тестовой таблицы Источник:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


Присоединиться и пропроектировать:

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

Результат:

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="creating-and-using-query-time-dimension-tables"></a>Создание и использование таблиц измерений времени запроса

Во многих случаях хочется присоединиться к результатам запроса с какой-либо специальной таблицей измерений, которая не хранится в базе данных. Можно определить выражение, результатом которого является таблица, прицела которой в один запрос, делая что-то вроде этого:

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

Вот несколько более сложный пример:

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>Получение последних (по метке времени) записей на личность

Предположим, у вас `id` есть таблица, которая включает столбец (определение сущности, с которой связана каждая строка, например, Идентификатор пользователя или идентификатор узлов) и `timestamp` столбец (обеспечивающий отсылку времени для строки), а также другие столбцы. Ваша цель состоит в том, чтобы написать запрос, `id` который возвращает последние 2 записи для каждого `timestamp`значения столбца, где "последний" определяется как "имеющий наивысшее значение".

Это можно сделать с помощью [топ-nested оператора.](topnestedoperator.md)
Сначала мы предоставляем запрос, а затем объясним:

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
1. Это `datatable` просто способ получения некоторых тестовых данных для демонстрационных целей. На самом деле, конечно, вы бы данные здесь.
2. Эта строка `id`по существу означает "вернуть все различные значения".
3. Затем эта строка возвращается для 2 `timestamp` записей, которые максимизируют столбец, столбцов предыдущего уровня (здесь, просто) `id`и столбца, указанного на этом уровне (здесь, `timestamp`).
4. Эта строка добавляет значения `bla` столбца для каждой из записей, возвращенных предыдущим уровнем. Если в таблице есть другие столбцы, представляющие интерес, можно было бы повторить эту строку для каждого такого столбца.
5. Наконец, мы используем [оператора проекта прочь](projectawayoperator.md) для удаления `top-nested`"лишних" столбцов, введенных .

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>Расширение таблицы с некоторыми процентами от общего расчета

Часто, когда у пользователя есть табло-выражение, включавввввввввавввав ежемерское столбец, хочется представить этот столбец пользователю вместе с его значением в процентах от общего числа. Например, предположим, что существует некоторый запрос, значение которого является следующей таблицей:

|SomeSeries|НекоторыеInt|
|----------|-------|
|Foo       |    100|
|линейчатая диаграмма.       |    200|

И вы хотите отобразить эту таблицу следующим образом:

|SomeSeries|НекоторыеInt|Pct |
|----------|-------|----|
|Foo       |    100|33.3|
|линейчатая диаграмма.       |    200|66.6|

Для этого необходимо вычислить общую (сумму) `SomeInt` столбца, а затем разделить каждое значение этого столбца на общую сумму. Это можно сделать для произвольных результатов, давая эти результаты имя, используя в [качестве оператора:](asoperator.md)

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

## <a name="performing-aggregations-over-a-sliding-window"></a>Выполнение агрегаций по раздвижным окну
В следующем примере показано, как обобщить столбцы с помощью раздвижного окна. Возьмем, к примеру, таблицу ниже, которая содержит цены на фрукты по меткам времени. Предположим, мы хотели бы рассчитать мин, максимальную и суммную стоимость каждого фрукта в день, используя раздвижное окно в 7 дней. Другими словами, каждая запись в установленном результате агрегирует последние 7 дней, и результат содержит рекорд в день в периоде анализа.  

Таблица фруктов: 

|Отметка времени|Фрукт|Price|
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

Раздвижной запрос агрегации окон (объяснение приведено ниже результатов запроса): 

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

Детали запроса: 

Запрос "растягивает" (дублирует) каждую запись в таблице ввода в течение 7 дней, размещая его фактический внешний вид, таким образом, что каждая запись на самом деле появляется 7 раз. В результате при выполнении агрегации в каждый день, агрегация включает в себя все записи предыдущих 7 дней.

Пошагзатое объяснение (цифры относятся к номерам в строках запроса):
1. Бин каждая запись до 1d (относительно _start). 
2. Определите конец диапазона на запись - _bin 7d, если это не вне _(начала, конца)_ диапазона, и в этом случае он корректируется. 
3. Для каждой записи создайте массив из 7 дней (временных меток), начиная с текущего дня записи. 
4. mv-расширить массив, тем самым дублируя каждую запись до 7 записей, 1 день отдельно друг от друга. 
5. Выполняйте функцию агрегирования для каждого дня. Из-за #4, это фактически суммирует _последние_ 7 дней. 
6. Наконец, поскольку данные за первые 7d являются неполными (в течение первых 7 дней нет 7d периода оглядки), мы исключаем первые 7 дней из конечного результата (они участвуют только в агрегации на 2018-10-01). 

## <a name="find-preceding-event"></a>Найти предыдущее событие
Следующий пример показывает, как найти предыдущее событие между двумя наборами данных.  

*Цель:* : Учитывая 2 набора данных, A и B, для каждой записи в B найти свое предыдущее событие в A (другими словами, arg_max запись в А, который по-прежнему "старше", чем B). Например, ниже приведен ожидаемый вывод для следующих наборов выборочных данных: 

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

|Отметка времени|Идентификатор|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ай2|

</br>

|Отметка времени|Идентификатор|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

Ожидаемые выходные данные: 

|Идентификатор|Отметка времени|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Для решения этой проблемы предложены 2 различных подхода. Вы должны проверить оба на вашем определенном наборе данных, чтобы найти один наиболее подходящий для вас (они могут выполнять по-разному на различных наборах данных). 

### <a name="suggestion-1"></a>Предложение #1:
Это предложение сериализирует как наборы данных по id и timestamp, а затем `arg_max` группирует все события B со всеми их предыдущими событиями А, и выбирает из всех Как в группе. 

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

### <a name="suggestion-2"></a>Предложение #2:
Это предложение требует определения периода максимального поиска (сколько "старше" мы позволяем запись в B?), а затем присоединяется к 2 наборов данных на Id и этот период поиска. Соединение производит все возможные кандидаты (все записи, которые старше B и в период обратного отчекани), а затем мы фильтруем ближайший к B по arg_min (TimestampB - TimestampA). Чем меньше период возврата, тем меньше, тем запрос будет работать лучше. 

В приведенном ниже примере период возврата установлен до 1 м, и поэтому запись с Id 'z' не имеет соответствующего события 'A' (поскольку это 'A' старше на 2 м).

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

|Идентификатор|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||