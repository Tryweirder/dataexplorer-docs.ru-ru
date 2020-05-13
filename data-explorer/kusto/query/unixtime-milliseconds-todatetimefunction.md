---
title: unixtime_milliseconds_todatetime () — обозреватель данных Azure
description: В этой статье описывается unixtime_milliseconds_todatetime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 26f1eb901798a28996c8cdc148fe68a71fb9d2b8
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370285"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

Преобразует миллисекунды в формате UNIX в формат UTC DateTime.

**Синтаксис**

`unixtime_milliseconds_todatetime(*milliseconds*)`

**Аргументы**

* *миллисекунды*: вещественное число представляет метку времени эпохи в миллисекундах. `Datetime`Это происходит до момента времени эпохи (1970-01-01 00:00:00) с отрицательным значением отметки времени.

**Возвращает**

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) . Если преобразование не выполнено, результатом будет NULL.

**См. также:**

* Преобразование секунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте микросекунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).
* Преобразование наносекунд в формате UNIX в формат UTC DateTime с помощью [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
