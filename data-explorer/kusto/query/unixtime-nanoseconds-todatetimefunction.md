---
title: unixtime_nanoseconds_todatetime () — обозреватель данных Azure
description: В этой статье описывается unixtime_nanoseconds_todatetime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: ca933f8d7ae61cbb0f49cf1e2226ffe51ba7bff7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241145"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

Преобразует наносекундах в формате UNIX в формат UTC DateTime.

## <a name="syntax"></a>Синтаксис

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

## <a name="arguments"></a>Аргументы

* *наносекундах*: вещественное число представляет метку времени эпохи в наносекундах. `Datetime` Это происходит до момента времени эпохи (1970-01-01 00:00:00) с отрицательным значением отметки времени.

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) . Если преобразование не выполнено, результатом будет NULL.

## <a name="see-also"></a>См. также раздел

* Преобразование секунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте миллисекунды в формате UNIX в формат UTC DateTime с помощью [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md).
* Преобразуйте микросекунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
