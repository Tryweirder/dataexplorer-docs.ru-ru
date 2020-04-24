---
title: Присоединение в течение временного окна - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается присоединение в течение временного окна в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aa3b81694714ef5af94407cdfdac263af0631e40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513368"
---
# <a name="joining-within-time-window"></a>Присоединение в течение временного окна

Часто полезно соединиться между двумя большими наборами данных на некоторых клавишах высокой степени кардинальности (например, идентификатор операции или идентификатор сеанса) и еще больше ограничить записи правой стороны ()`$right`записи, которые должны быть сопоставлены для каждой левой стороны ()`$left`записи, добавив ограничение на "расстояние времени" между `datetime` столбиками слева и справа. Это отличается от обычной операции присоединения Kusto, так как, в дополнение к части «экви-соединения» (соответствует клавише высокой степени кардиналити или набору данных слева и справа), система также может применить функцию расстояния и использовать ее для ускорения соединения значительно. Обратите внимание, что функция расстояния не ведет себя `dist(x,y)` как `dist(y,z)` равенство (т.е. когда и то, и другое и верно, это не так. `dist(x,z)` *Внутренне мы иногда называем это «диагональным соединением».*

Например, предположим, что мы хотим определить последовательности событий в относительно небольшом временном окне. Чтобы продемонстрировать этот пример, `T` предположим, что у нас есть таблица со следующей схемой:

- `SessionId`: столбец `string` типа с идичтов корреляции.
- `EventType`: столбец `string` типа, который определяет тип события записи.
- `Timestamp`: Столбец `datetime` типа, указывающий, когда произошло событие, описанное записью.

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

   Найдите все иные ики `A` сессии, в `B` `1min` каком типе события последовал тип события в течение временного окна.

(В приведенных выше данных выборки единственным идентификатором сеанса является `0`.)

Семантически на этот вопрос отвечает следующий вопрос, хотя и неэффективно:

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

Чтобы оптимизировать этот запрос, мы можем переписать его, как описано ниже, так что временное окно выражается как ключ соединения.

**Переписывание запроса для учета временного окна**

Идея состоит в том, чтобы переписать запрос так, чтобы `datetime` значения были «дискретизированы» в ведра, размер которых в два раза меньше временного окна.
Затем мы можем использовать экви-соединение Кусто, чтобы сравнить эти идоколы ведра.

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


**Ссылка на запрос runnable (с внижаемой таблицей)**

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


**50M запрос данных**

Следующий запрос эмулирует набор данных из 50M-записей и идентификаций на 10 ММ и выполняет запрос с описанным выше методом.

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