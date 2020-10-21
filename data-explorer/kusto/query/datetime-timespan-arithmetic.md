---
title: Арифметические действия с DateTime/TimeSpan-Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается арифметические операции с DateTime/TimeSpan в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 0b70cb1730d893e07790ade3079be21da209648c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247688"
---
# <a name="datetime--timespan-arithmetic"></a>Арифметические операции с DateTime/TimeSpan

Kusto поддерживает выполнение арифметических операций над значениями типов `datetime` и `timespan` :

* Один из них может вычитать (но не добавлять) два `datetime` значения, чтобы получить `timespan` значение, выражающий их разность.
  Например, `datetime(1997-06-25) - datetime(1910-06-11)` как старое было [Жак-Ивес каустеау](https://en.wikipedia.org/wiki/Jacques_Cousteau) , когда умер.

* Можно добавить или вычесть два `timespan` значения, чтобы получить `timespan` значение, которое является их суммой или разностью.
  Например, `1d + 2d` имеет три дня.

* Одно из значений может добавлять или вычитать `timespan` значение `datetime` .
  Например, `datetime(1910-06-11) + 1d` — это дата, каустеау на один день назад.

* Один может разделить два `timespan` значения, чтобы получить их частное.
  Например, `1d / 5h` дает `4.8` .
  Это дает одной возможности выразить любое `timespan` значение как кратное другому `timespan` значению. Например, чтобы выразить час в секундах, просто разделите `1h` на `1s` : `1h / 1s` (с очевидным результатом `3600` ).

* И наоборот, одно из значений может иметь несколько числовых значений (например `double` , и `long` ), `timespan` чтобы получить `timespan` значение.
  Например, один из них может выражать час и половину от `1.5 * 1h` .

## <a name="example-unix-time"></a>Пример: время UNIX

[Время UNIX](https://en.wikipedia.org/wiki/Unix_time) (также известное как время POSIX или время работы в среде UNIX) — это система для описания момента времени в виде числа секунд, прошедших с 00:00:00 четверг, 1 января 1970, время в формате UTC, минус високосные секунды.

Если данные включают представление времени Unix в виде целого числа или требуется преобразование в него, доступны следующие функции:

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|набор по                     |
|---------------------------|
|2019-01-07 21:45:31.0000000|

```kusto
let toUnixTime = (dt:datetime) 
{ 
    (dt - datetime(1970-01-01)) / 1s 
};
print result = toUnixTime(datetime(2019-01-07 21:45:31.0000000))
```

|набор по                     |
|---------------------------|
|1546897531                 |

> [!NOTE]
> В дополнение к функциям, приведенным выше, ознакомьтесь с разделом выделенные функции для преобразования времени в формате UNIX: [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md) 
>  [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md) 
>  [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md) 
>  [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md)