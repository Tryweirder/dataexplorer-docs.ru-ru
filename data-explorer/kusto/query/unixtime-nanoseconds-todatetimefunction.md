---
title: unixtime_nanoseconds_todatetime () — обозреватель данных Azure
description: В этой статье описывается unixtime_nanoseconds_todatetime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 869a89510e96c1c3fbc99feb9a01bd70d2728149
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103459"
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
