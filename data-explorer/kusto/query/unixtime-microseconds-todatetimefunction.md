---
title: unixtime_microseconds_todatetime () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны unixtime_microseconds_todatetime () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 6ab593049fcafeba27b8158a1c9c6a5b6fa75fe3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505276"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Преобразует микросекунды unix-эпохи в время дат UTC.

**Синтаксис**

`unixtime_microseconds_todatetime(*microseconds*)`

**Аргументы**

* *микросекунды*: Реальное число представляет собой эпоха timestamp в микросекундах. `Datetime`что происходит до эпохи времени (1970-01-01 00:00:00) имеет отрицательное значение метки времени.

**Возвращает**

Если конверсия выполнена успешно, результатом будет значение [времени даты.](./scalar-data-types/datetime.md) Если конверсия не будет успешной, результат будет нулевым.

**См. также:**

* Преобразуйте секунды unix-эпохи в время дат UTC, используя [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте миллисекунды unix-эпохи в utC datetime с помощью [unixtime_milliseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md).
* Преобразуйте наносекунды unix-эпохи в время дат UTC с помощью [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md).

**Пример**

```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|