---
title: Присоединение в течение временного интервала Azure обозреватель данных
description: В этой статье описывается объединение в течение временного интервала в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ca7f38fa377be40cd290b04af65cc6fd59075cd
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763718"
---
# <a name="time-window-join"></a>Соединение по периоду времени

Часто бывает полезно объединить два больших набора данных в некоторый ключ высокой кратности, например, идентификатор операции или идентификатор сеанса, а также ограничение правой части ($right) записей, которые необходимо сопоставить с каждой левой записью ($left), добавив ограничение на расстояние времени между столбцами datetime слева и справа.

Функция полезна в соединении, как в следующем сценарии:
* Объединение двух больших наборов данных в соответствии с ключом высокой кратности, например ИДЕНТИФИКАТОРом операции или ИДЕНТИФИКАТОРом сеанса.
* Ограничьте записи правой части ($right), которые должны сопоставляться с каждой левой записью ($left), добавив ограничение на расстояние времени между столбцами datetime слева и справа.

Указанная выше операция отличается от обычной операции соединения Kusto, поскольку для *`equi-join`* части сопоставления ключа высокой кратности между левым и правым наборами данных система также может применить функцию расстояния и использовать ее для значительной ускорения соединения.

> [!NOTE]
> Функция расстояния не работает как равенство (т. е. когда значение расп (x, y) и расп (y, z) равно true, это не относится к значению "расп" (x, z).) На внутреннем уровне мы иногда называем «диагональным соединением».

Например, если требуется определить последовательности событий в относительно небольшом временном окне, предположим, что имеется таблица `T` со следующей схемой:

* `SessionId`: Столбец типа `string` с идентификаторами корреляции.
* `EventType`: Столбец типа `string` , определяющий тип события записи.
* `Timestamp`: Столбец типа `datetime` указывает, когда произошло событие, описываемое записью.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
T
```

|SessionId|EventType|Отметка времени|
|---|---|---|
|0|Объект|2017-10-01 00:00:00.0000000|
|0|B|2017-10-01 00:01:00.0000000|
|1|B|2017-10-01 00:02:00.0000000|
|1|Объект|2017-10-01 00:03:00.0000000|
|3|Объект|2017-10-01 00:04:00.0000000|
|3|B|2017-10-01 00:10:00.0000000|


**Проблема**

Наш запрос должен ответить на следующий вопрос:

   Найдите все идентификаторы сеансов, в которых за типом события `A` следует тип события `B` в течение временного интервала `1min` .

> [!NOTE]
> В приведенном выше образце данных единственным ИДЕНТИФИКАТОРом сеанса является `0` .

В семантическом случае следующий запрос отвечает на этот вопрос, хотя и неэффективно.

```kusto
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp
    ) on SessionId
| where (End - Start) between (0min .. 1min)
| project SessionId, Start, End 

```

|SessionId|Начать|Конец|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

Чтобы оптимизировать этот запрос, можно переписать его, как описано ниже, чтобы временное окно выражается как ключ объединения.

**Перепишите запрос для учета временного интервала.**

Перепишите запрос таким образом, чтобы `datetime` значения «дискретизировать» в сегменты, размер которых составляет половину размера временного окна. Используйте Kusto *`equi-join`* для сравнения идентификаторов контейнеров.

```kusto
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0; // lookup bin = equal to 1/2 of the lookup window
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp,
          // TimeKey on the left side of the join is mapped to a discrete time axis for the join purpose
          TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              // TimeKey on the right side of the join - emulates event 'B' appearing several times
              // as if it was 'replicated'
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    // 'mv-expand' translates the TimeKey array range into a column
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|Начать|Конец|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

**Ссылка на готовый запрос (со встроенной таблицей)**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp, TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|Начать|Конец|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|


**запрос данных 50 млн**

Следующий запрос эмулирует набор данных записей 50 млн и идентификаторы ~ 10 миллионов и выполняет запрос с помощью описанного выше метода.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = range x from 1 to 50000000 step 1
| extend SessionId = rand(10000000), EventType = rand(3), Time=datetime(2017-01-01)+(x * 10ms)
| extend EventType = case(EventType <= 1, "A",
                          EventType <= 2, "B",
                          "C");
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Time, TimeKey = bin(Time, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Time, 
              TimeKey = range(bin(Time-lookupWindow, lookupBin), 
                              bin(Time, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
| count 
```

|Count|
|---|
|1276|
