---
title: unixtime_seconds_todatetime () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны unixtime_seconds_todatetime () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 74abf66dabb7a8fc74085c695081d6a11bfdf3a3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505225"
---
# <a name="unixtime_seconds_todatetime"></a>unixtime_seconds_todatetime()

Преобразует секунды unix-эпохи в время даты UTC.

**Синтаксис**

`unixtime_seconds_todatetime(*seconds*)`

**Аргументы**

* *секунд*: Реальное число представляет собой эпоха timestamp в секундах. `Datetime`что происходит до эпохи времени (1970-01-01 00:00:00) имеет отрицательное значение метки времени.

**Возвращает**

Если конверсия выполнена успешно, результатом будет значение [времени даты.](./scalar-data-types/datetime.md) Если конверсия не будет успешной, результат будет нулевым.

**См. также:**

* Преобразуйте миллисекунды unix-эпохи в utC datetime с помощью [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md).
* Преобразуйте микросекунды unix-эпохи в время дат UTC с помощью [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md).
* Преобразуйте наносекунды unix-эпохи в время дат UTC с помощью [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md).

**Пример**

```kusto
print date_time = unixtime_seconds_todatetime(1546300800)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|