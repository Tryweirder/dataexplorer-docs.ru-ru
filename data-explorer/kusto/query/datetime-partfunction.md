---
title: datetime_part () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны datetime_part () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c64208725f0d5c49a7ea7733f8eb5a208e19225b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516377"
---
# <a name="datetime_part"></a>datetime_part()

Извлекает запрашиваемую часть даты в качестве значения целых.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

**Синтаксис**

`datetime_part(`*время даты части*`,`*datetime*`)`

**Аргументы**

* `date`: `datetime`
* `part`: `string`

Возможные `part`значения: 
- Год
- Квартал
- Месяц
- week_of_year
- День
- DayOfYear
- Час
- Минута
- Секунда
- Миллисекунда
- Мкс
- Наносекундных

**Возвращает**

Цель, представляющая извлеченную часть.

**Примечание**

`week_of_year`возвращает целый ряд, который представляет номер недели. Недельное число рассчитывается с первой недели года, которая включает в себя первый четверг.

**Примеры**

```kusto
let dt = datetime(2017-10-30 01:02:03.7654321); 
print 
year = datetime_part("year", dt),
quarter = datetime_part("quarter", dt),
month = datetime_part("month", dt),
weekOfYear = datetime_part("week_of_year", dt),
day = datetime_part("day", dt),
dayOfYear = datetime_part("dayOfYear", dt),
hour = datetime_part("hour", dt),
minute = datetime_part("minute", dt),
second = datetime_part("second", dt),
millisecond = datetime_part("millisecond", dt),
microsecond = datetime_part("microsecond", dt),
nanosecond = datetime_part("nanosecond", dt)

```

|year|квартал|month|WeekOfYear|day|dayOfYear|hour|minute|second|миллисекунда|микросекунда|наносекунда|
|---|---|---|---|---|---|---|---|---|---|---|---|
|2017|4|10|44|30|303|1|2|3|765|765432|765432100|

> [!NOTE]
> `weekofyear`является устаревшим `week_of_year` вариантом детали. `weekofyear`ISO 8601 не соответствовала требованиям; первая неделя года была определена как неделя с первой средой года в нем.
`week_of_year`ISO 8601 соответствует требованиям; первая неделя года определяется как неделя с первым четвергом года в нем. [Для получения дополнительной информации](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).