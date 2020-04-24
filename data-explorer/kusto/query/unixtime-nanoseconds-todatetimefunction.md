---
title: unixtime_nanoseconds_todatetime () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны unixtime_nanoseconds_todatetime () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: cd9dadca7ab4455f8a90d5a7842293b2d6c8bcc2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505242"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

Преобразует наносекунды эпохи unix в utC.

**Синтаксис**

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

**Аргументы**

* *наносекунды*: Реальное число представляет собой эпоха timestamp в наносекундах. `Datetime`что происходит до эпохи времени (1970-01-01 00:00:00) имеет отрицательное значение метки времени.

**Возвращает**

Если конверсия выполнена успешно, результатом будет значение [времени даты.](./scalar-data-types/datetime.md) Если конверсия не будет успешной, результат будет нулевым.

**См. также:**

* Преобразуйте секунды unix-эпохи в время дат UTC, используя [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте миллисекунды unix-эпохи в utC datetime с помощью [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md).
* Преобразуйте микросекунды unix-эпохи в время дат UTC с помощью [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md).

**Пример**

```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|