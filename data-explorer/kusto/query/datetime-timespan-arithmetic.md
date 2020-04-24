---
title: Арифметика дат/промежутка времени - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается арифметика Datetime / timespan в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 34bda7b8418dd519995f25c625a5031202a64a8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516326"
---
# <a name="datetime--timespan-arithmetic"></a>Арифметика дат / временной промежутки

Kusto поддерживает выполнение арифметических операций `datetime` `timespan`по значениям типов и:

* Можно вычесть (но не `datetime` добавить) два `timespan` значения, чтобы получить значение, выражающее их разницу.
  Например, `datetime(1997-06-25) - datetime(1910-06-11)` сколько лет было, когда он умер, ему было [юака-Ива Кусто.](https://en.wikipedia.org/wiki/Jacques_Cousteau)

* Можно добавить или `timespan` вычесть два `timespan` значения, чтобы получить значение, которое является их суммой или разницей.
  Например, `1d + 2d` три дня.

* Можно добавить или `timespan` вычесть `datetime` значение из значения.
  Например, `datetime(1910-06-11) + 1d` это дата Кусто исполнилось один день.

* Можно разделить `timespan` два значения, чтобы получить их коэффициент.
  Например, `1d / 5h` `4.8`дает .
  Это дает возможность выразить `timespan` любое значение как `timespan` кратное другое значение. Например, чтобы выразить час в `1h` секундах, просто разделить на `1s`: `1h / 1s` (с очевидным результатом, `3600`).

* И наоборот, можно несколько числового `long`значения `timespan` (например, `timespan` `double` и) значение, чтобы получить значение.
  Например, можно выразить полтора часа, `1.5 * 1h`как .

## <a name="example-unix-time"></a>Пример: время Unix

[Unix время](https://en.wikipedia.org/wiki/Unix_time) (также известный как ВРЕМЯ POSIX или UNIX Epoch время) представляет собой систему для описания точки во времени, как количество секунд, которые прошли с 00:00:00 четверг, 1 января 1970 года, координированное универсальное время (UTC), минус високосные секунды.

Если ваши данные включают представление времени Unix в качестве целых числа или требуется преобразование в него, доступны следующие функции:

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
> В дополнение к вышеуказанным функциям см. выделенные функции для преобразований времени unix-эпохи: [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)
> [unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md)
> [unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md)
> [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)