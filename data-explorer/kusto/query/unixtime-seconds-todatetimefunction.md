---
title: unixtime_seconds_todatetime () — обозреватель данных Azure
description: В этой статье описывается unixtime_seconds_todatetime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 87a2db1109fecfd7e27f29d4305449b596f7cd68
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370412"
---
# <a name="unixtime_seconds_todatetime"></a>unixtime_seconds_todatetime()

Преобразует секунды в формате UNIX в формат UTC DateTime.

**Синтаксис**

`unixtime_seconds_todatetime(*seconds*)`

**Аргументы**

* *секунды*: вещественное число представляет метку времени эпохи в секундах. `Datetime`Это происходит до момента времени эпохи (1970-01-01 00:00:00) с отрицательным значением отметки времени.

**Возвращает**

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) . Если преобразование не выполнено, результатом будет NULL.

**См. также:**

* Преобразуйте миллисекунды в формате UNIX в формат UTC DateTime с помощью [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Преобразуйте микросекунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).
* Преобразование наносекунд в формате UNIX в формат UTC DateTime с помощью [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_seconds_todatetime(1546300800)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
