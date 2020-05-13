---
title: unixtime_microseconds_todatetime () — обозреватель данных Azure
description: В этой статье описывается unixtime_microseconds_todatetime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: f69261e7ac84d8ae77fd1d2fb89f31ede2536443
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370239"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Преобразует микросекунды из UNIX-эпохи в формат UTC DateTime.

**Синтаксис**

`unixtime_microseconds_todatetime(*microseconds*)`

**Аргументы**

* *микросекунды*: вещественное число представляет метку времени эпохи в микросекундах. `Datetime`Это происходит до момента времени эпохи (1970-01-01 00:00:00) с отрицательным значением отметки времени.

**Возвращает**

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) . Если преобразование не выполнено, результатом будет NULL.

**См. также:**

* Преобразование секунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте миллисекунды в формате UNIX в формат UTC DateTime с помощью [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Преобразование наносекунд в формате UNIX в формат UTC DateTime с помощью [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
