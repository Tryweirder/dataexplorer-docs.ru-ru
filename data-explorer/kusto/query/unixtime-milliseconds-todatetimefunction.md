---
title: unixtime_milliseconds_todatetime () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны unixtime_milliseconds_todatetime () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: ab229d78d2a9ff5a7e50ecefe027824488578b12
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505259"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

Преобразует миллисекунды unix-эпохи в utC datetime.

**Синтаксис**

`unixtime_milliseconds_todatetime(*milliseconds*)`

**Аргументы**

* *миллисекунды*: Реальное число представляет собой эпоха timestamp в миллисекундах. `Datetime`что происходит до эпохи времени (1970-01-01 00:00:00) имеет отрицательное значение метки времени.

**Возвращает**

Если конверсия выполнена успешно, результатом будет значение [времени даты.](./scalar-data-types/datetime.md) Если конверсия не будет успешной, результат будет нулевым.

**См. также:**

* Преобразуйте секунды unix-эпохи в время дат UTC, используя [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте микросекунды unix-эпохи в время дат UTC с помощью [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md).
* Преобразуйте наносекунды unix-эпохи в время дат UTC с помощью [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md).

**Пример**

```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|