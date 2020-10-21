---
title: datetime_diff () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается datetime_diff () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 50ed62b60436fc13d679b5e729a84bcdcefa7275
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247707"
---
# <a name="datetime_diff"></a>datetime_diff()

Вычисляет календариан разницу между двумя значениями [типа DateTime](./scalar-data-types/datetime.md) .

## <a name="syntax"></a>Синтаксис

`datetime_diff(`*период времени* `,` *datetime_1* `,` *datetime_2*`)`

## <a name="arguments"></a>Аргументы

* `period`: `string`. 
* `datetime_1`: значение [DateTime](./scalar-data-types/datetime.md) .
* `datetime_2`: значение [DateTime](./scalar-data-types/datetime.md) .

Возможные значения *периода*: 
- Year
- Quarter
- Месяц
- Неделя
- День
- Hour
- Минута
- Second
- Миллисекунда
- Микросекунды
- Потребовавшееся

## <a name="returns"></a>Результаты

Целое число, представляющее величину `periods` результата вычитания ( `datetime_1`  -  `datetime_2` ).

## <a name="examples"></a>Примеры

```kusto
print
year = datetime_diff('year',datetime(2017-01-01),datetime(2000-12-31)),
quarter = datetime_diff('quarter',datetime(2017-07-01),datetime(2017-03-30)),
month = datetime_diff('month',datetime(2017-01-01),datetime(2015-12-30)),
week = datetime_diff('week',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
day = datetime_diff('day',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
hour = datetime_diff('hour',datetime(2017-10-31 01:00),datetime(2017-10-30 23:59)),
minute = datetime_diff('minute',datetime(2017-10-30 23:05:01),datetime(2017-10-30 23:00:59)),
second = datetime_diff('second',datetime(2017-10-30 23:00:10.100),datetime(2017-10-30 23:00:00.900)),
millisecond = datetime_diff('millisecond',datetime(2017-10-30 23:00:00.200100),datetime(2017-10-30 23:00:00.100900)),
microsecond = datetime_diff('microsecond',datetime(2017-10-30 23:00:00.1009001),datetime(2017-10-30 23:00:00.1008009)),
nanosecond = datetime_diff('nanosecond',datetime(2017-10-30 23:00:00.0000000),datetime(2017-10-30 23:00:00.0000007))
```

|year|квартал|month|week|day|hour|minute|second|миллисекунда|микросекунда|наносекунда|
|---|---|---|---|---|---|---|---|---|---|---|
|17|2|13|5|29|2|5|10|100|100|— 700|



