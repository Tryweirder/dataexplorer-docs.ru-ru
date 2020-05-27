---
title: Управление командами — Azure обозреватель данных
description: В этой статье описывается управление командами в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e6a31e2c79ae658cceecfdad0ce307e2522bb55b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011420"
---
# <a name="commands-management"></a>Управление командами

## <a name="show-commands"></a>. показывать команды 

`.show commands`Возвращает таблицу с командами администрирования, которые достигают конечного состояния. Эти команды можно запросить в течение 30 дней.

Таблица Commands содержит два столбца со сведениями о потреблении ресурсов для каждой выполненной команды.

* Тоталкпу — общее время ЦП (пользовательский режим + режим ядра), используемое этой командой.
* Ресаурцеутилизатион — содержит все сведения об использовании ресурсов, связанные с этой командой, включая Тоталкпу.

Потребляемое потребление ресурсов включает обновления данных и любой запрос, связанный с текущей командой администратора.
В настоящее время в таблице Commands (,,,,) рассматриваются только некоторые команды администрирования `.ingest` `.set` `.append` `.set-or-replace` `.set-or-append` . Постепенно в таблицу Commands будут добавлены дополнительные команды.

* [Администратор базы данных или монитор базы данных](../management/access-control/role-based-authorization.md) может видеть любую команду, которая была вызвана в своей базе данных.
* Другие пользователи могут видеть только те команды, которые были им вызваны.

**Синтаксис**

`.show` `commands`
 
**Пример**
 
|клиентактивитид |CommandType |Текст |База данных |стартедон |ластупдатедон |Длительность |Состояние |RootActivityId |Пользователь |FailureReason |Развертывание |Основной |тоталкпу |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |екстентсмерже   |. слияние асинхронных операций...    |БД1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Завершено  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |ИД приложения AAD = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM. svc |аадапп = 5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |{"Сканнедекстентсстатистикс": {"Миндатасканнедтиме": NULL, "Максдатасканнедтиме": NULL}, "Качестатистикс": {Memory ": {" промахи ": 2," попадания ": 20}," Disk ": {" промахи ": 2," попаданий ": 0}}," MemoryPeak ": 159620640," TotalCpu ":" 00:00:03.5781250 "} 
|KE. Рункомманд; 710e08ca-2cd3-4d2d-b7bd-2738d335aa50    |датаинжестпулл |. прием в Митабленаме...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |Failed |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |Подключение к сокету было ликвидировано.   |Kusto.Explorer |аадусер =...    |00:00:00   |{"Сканнедекстентсстатистикс": {"Миндатасканнедтиме": NULL, "Максдатасканнедтиме": NULL}, "Качестатистикс": {"память": {"промахи": 0, попадания ": 0}," диск ": {" промахи ": 0," попаданий ": 0}}," MemoryPeak ": 0," TotalCpu ":" 00:00:00 "} 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |екстентсребуилд |. слияние асинхронных операций...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Завершено  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |ИД приложения AAD = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM. svc |аадапп = 5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |{"Сканнедекстентсстатистикс": {"Миндатасканнедтиме": NULL, "Максдатасканнедтиме": NULL}, "Качестатистикс": {Memory ": {" промахи ": 0," попадания ": 1}," Disk ": {" промахи ": 0," попаданий ": 0}}," MemoryPeak ": 88828560," TotalCpu ":" 00:00:00.8906250 "} 

**Пример. Извлечение конкретных данных из столбца Ресаурцеутилизатион**

Доступ к одному из свойств в столбце Ресаурцеутилизатион осуществляется путем вызова ResourcesUtilization.xxx (где XXX — имя свойства).
> [!NOTE] 
> `ResourceUtilization`является динамическим столбцом. Для работы со своими значениями необходимо сначала преобразовать его в конкретный тип данных. Используйте функцию преобразования `tolong` , например, `toint` , `totimespan` .  

Например, примененная к объекту директива

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|стартедон |меморипеак |тоталкпу |Текст
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500 |. Добавьте Server_Boots <\| let бутстартссаурцетабле = сессионстартс;...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750 |. Set-или-Append <\| базы данных ("куратеддб"). WebUsage_v2 | суммировать... | проект...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000 |. Set-или-Append Атласклустеревентстатс с (...) <\| Atlas_Temp (DateTime (2017-09-28 12:13:28.7621737), DateTime (2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>Исследование проблем с производительностью

`.show``commands`можно использовать для изучения проблем с производительностью, так как они показывают ресурсы, используемые каждой командой управления.

Ниже приведены простые и полезные запросы для такого исследования.

### <a name="query-the-totalcpu-column"></a>Запрос столбца Тоталкпу

10 наиболее ресурсоемких запросов ЦП за последний день.

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

Все запросы за последние 10 часов, Тоталкпу которых прошло 3 минуты.

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

Все запросы за последние 24 часа, Тоталкпу которых прошло 5 минут, сгруппированные по пользователю и участнику.

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

Диаграмму: средняя загрузка ЦП в сравнении с 95 процентиль процентиль и Max CPU.

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="query-the-memorypeak"></a>Запрос Меморипеак

Первые 10 запросов за последний день с самыми высокими значениями Меморипеак.

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

Диаграмму среднего Меморипеак VS 95 процентиль процентиль и Max Меморипеак.

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
