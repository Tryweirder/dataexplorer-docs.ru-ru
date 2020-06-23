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
ms.openlocfilehash: d23d76fdcc592435a8ec7fa24ef5d0dfd5186c68
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128478"
---
# <a name="time-window-join"></a>Соединение по периоду времени

Часто бывает полезно объединить два больших набора данных в некоторый ключ высокой кратности (например, идентификатор операции или идентификатор сеанса) и еще больше ограничить права `$right` , которые необходимо сопоставить для каждой левой () записи (), `$left` добавив ограничение на расстояние времени между `datetime` столбцами слева и справа от левого края (). Это отличается от обычной операции соединения Kusto, как в дополнение к части «эквивалентное соединение» (совпадающей с ключом высокой кратности или левому и правому набору данных), система также может применить функцию расстояния и использовать ее для значительного ускорения соединения. Обратите внимание, что функция расстояния не работает как равенство (т. е `dist(x,y)` . Если оба `dist(y,z)` значения и имеют истинное значение, это `dist(x,z)` также верно.) *На внутреннем уровне мы иногда называем «диагональным соединением».*

Например, предположим, что нужно определить последовательности событий в относительно небольшом временном интервале. Чтобы продемонстрировать этот пример, предположим, что у нас есть таблица `T` со следующей схемой:

- `SessionId`: Столбец типа `string` с идентификаторами корреляции.
- `EventType`: Столбец типа `string` , определяющий тип события записи.
- `Timestamp`: Столбец типа `datetime` , указывающий, когда произошло событие, описываемое записью.

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

Мы хотим, чтобы наш запрос ответил на следующий вопрос:

   Найдите все идентификаторы сеансов, в которых за типом событий `A` следует тип события `B` в течение `1min` временного интервала.

(В приведенном выше образце данных единственным ИДЕНТИФИКАТОРом сеанса является `0` .)

В семантическом виде следующий запрос отвечает на этот вопрос, хотя и неэффективно:

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

|SessionId|Запуск|Конец|
|---|---|---|
|0|2017-10-01 00:00:00.0000000|2017-10-01 00:01:00.0000000|

Чтобы оптимизировать этот запрос, можно переписать его, как описано ниже, чтобы временное окно выражается как ключ объединения.

**Перезапись запроса для учета временного интервала**

Идея заключается в перезаписи запроса, чтобы `datetime` значения были "дискретны" в сегменты, размер которых составляет половину размера временного окна.
Затем можно использовать эквивалентное соединение Kusto для сравнения этих идентификаторов контейнеров.

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

|SessionId|Запуск|Конец|
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

|SessionId|Запуск|Конец|
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
