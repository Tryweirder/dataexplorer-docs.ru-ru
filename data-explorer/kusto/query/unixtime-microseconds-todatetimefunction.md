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
ms.openlocfilehash: e4d97aac09138c0268bc5c466c67f64cd1da6ebe
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350627"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Преобразует микросекунды из UNIX-эпохи в формат UTC DateTime.

## <a name="syntax"></a>Синтаксис

`unixtime_microseconds_todatetime(*microseconds*)`

## <a name="arguments"></a>Аргументы

* *микросекунды*: вещественное число представляет метку времени эпохи в микросекундах. `Datetime`Это происходит до момента времени эпохи (1970-01-01 00:00:00) с отрицательным значением отметки времени.

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) . Если преобразование не выполнено, результатом будет NULL.

**См. также:**

* Преобразование секунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте миллисекунды в формате UNIX в формат UTC DateTime с помощью [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Преобразование наносекунд в формате UNIX в формат UTC DateTime с помощью [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
