---
title: unixtime_milliseconds_todatetime () — обозреватель данных Azure
description: В этой статье описывается unixtime_milliseconds_todatetime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 9f029b18a147907a9e77e5ade7a1b708d3bed877
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245657"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

Преобразует миллисекунды в формате UNIX в формат UTC DateTime.

## <a name="syntax"></a>Синтаксис

`unixtime_milliseconds_todatetime(*milliseconds*)`

## <a name="arguments"></a>Аргументы

* *миллисекунды*: вещественное число представляет метку времени эпохи в миллисекундах. `Datetime` Это происходит до момента времени эпохи (1970-01-01 00:00:00) с отрицательным значением отметки времени.

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) . Если преобразование не выполнено, результатом будет NULL.

## <a name="see-also"></a>См. также раздел

* Преобразование секунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md).
* Преобразуйте микросекунды UNIX-эпохи в формат UTC DateTime с помощью [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md).
* Преобразование наносекунд в формате UNIX в формат UTC DateTime с помощью [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md).

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
