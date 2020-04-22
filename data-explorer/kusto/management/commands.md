---
title: Управление командами - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление командами в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5685833431529af22aa4d8778d121f5a4dec16d1
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744310"
---
# <a name="commands-management"></a>Управление командами

## <a name="show-commands"></a>.show команды 

`.show``commands` команда возвращает таблицу с командами админа, которые достигли конечного состояния. Эти команды доступны для запроса в течение 30 дней.

Таблица команд имеет две колонки с сведениями о потреблении ресурсов каждой завершенной команды:
* TotalCpu: Общее время часы процессора (режим пользователя и режим ядра), потребляемые этой командой.
* Унитилизация ресурсов: объект, содержащий всю информацию об использовании ресурсов, связанную с этой командой (включая TotalCpu).

Отслеживаемые расходы ресурсов включают обновления данных, а также любой запрос, связанный с текущей командой Admin.
В настоящее время только некоторые команды Admin охватываются таблицей команд (.ingest, .set, .append, .set-or-replace, .set-or-append), и постепенно в будущем будет добавлено больше команд.


* [Админ базы данных или монитор базы данных](../management/access-control/role-based-authorization.md) могут видеть любую команду, которая была вызвана в их базе данных.
* Другие пользователи могут видеть только команды, которые были вызваны ими.

**Синтаксис**

`.show` `commands`
 
**Пример**
 
|ClientActivityId |CommandType |текст |База данных |Startedon |LastUpdatedon |Duration |Состояние |RootActivityId |Пользователь |FailureReason |Приложение |Основной |TotalCpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |ПровизияMerge   |.merge async Операции ...    |БД1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Завершено  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |Приложение AAD id'5ba8cec2-9a70-e92c98cad651  |   |Кусто.Azure.DM.Svc |aadapp-5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |"ScannedExtentsStatistics": " MinDataScannedTime": null, "MaxDataScannedTime": null, "CacheStatistics": "Память": "Промахи": 2, "Хиты": 20, "Диск": "Промахи": 2, "Хиты": 0 , "MemoryPeak", "TotalCpu": "00:00:03.578120" 
|Ке. RunCommand;710e08ca-2cd3-4d2d-b7bd-2738d335aa50 |DataIngestPull |.ingest в MyTableName ...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |Ошибка |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |Соединение розетки утилизировано.   |Kusto.Explorer |aaduser...    |00:00:00   |"ScannedExtentsStatistics": "MinDataScannedTime": null, "MaxDataScannedTime": null , "CacheStatistics": "Memory": "Промахи": 0, Хиты:0, "Диск": "Промахи": 0, "Хиты": 0 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |ПровизияВосстановление |.merge async Операции ...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Завершено  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |Приложение AAD id'5ba8cec2-9a70-e92c98cad651  |   |Кусто.Azure.DM.Svc |aadapp-5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |"ScannedExtentsStatistics": " MinDataScannedTime": null, "MaxDataScannedTime": null , "CacheStatistics": "Память": "Промахи": 0, "Хиты": 1 , "Диск": "Промахи": 0, "Хиты": 0, "Хиты": 0 , "MemoryPeak": 88828560, "TotalCpu": "00:00:00.8990620" 

**Пример: извлечение конкретных данных из столбца ресурсообразования**

Доступ к одному из свойств в столбце ResourceUtilization осуществляется путем вызова ResourcesUtilization.xxx (где xxx является именем свойства).
Обратите внимание, что унитилизация ресурсов является динамическим столбцом, и, следовательно, для того, чтобы работать с его значениями, следует сначала преобразовать его в определенный тип данных (с использованием функции преобразования, такие как: tolong, toint, totimespan, ...).  

Пример:

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|Startedon |MemoryPeak |TotalCpu |текст
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500  | .append Server_Boots \| <пусть bootStartsSourceTable и SessionStarts; ...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750  | .set-or-append WebUsage \| <базе данных ('CuratedDB'). WebUsage_v2 | Суммировать... | Проекта...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000  | .set-or-append AtlasClusterEventStats с(..) <\| Atlas_Temp (дата (2017-09-28 12:13:28.7621737), дата (2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>Изучение проблем производительности

`.show``commands` могут быть использованы для изучения проблем с производительностью, так как они позволяют видеть ресурсы, потребляемые каждым командованием управления.
Следующие примеры являются простыми и полезными запросами для таких расследований.

### <a name="querying-the-totalcpu-column"></a>Запрос столбца TotalCpu

Топ 10 запросов на потребление процессора за последний день:

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

Все запросы за последние 10 часов, которые их TotalCpu пересек 3 минуты:

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

Все запросы за последние 24 часа, которые их TotalCpu был выше 5 минут, сгруппированы пользователем и основным:

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

Таймчарт: Средний процессор против 95-го процентиля против процессора Max:

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="querying-the-memorypeak"></a>Запрос памятиПик

Топ-10 запросов за последний день с самыми высокими значениями MemoryPeak:

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

Тайм-чарт средней памятиПик против 95-го процентиля против Max MemoryPeak:

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
