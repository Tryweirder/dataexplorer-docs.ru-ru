---
title: datetime_part () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается datetime_part () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 2c1a73d2d7e31eb180b37fae3d392fd5792cd69b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348519"
---
# <a name="datetime_part"></a>datetime_part()

Извлекает запрошенную часть даты как целочисленное значение.

```kusto
datetime_part("Day",datetime(2015-12-14))
```

## <a name="syntax"></a>Синтаксис

`datetime_part(`*часть* `,` *Дата и время*`)`

## <a name="arguments"></a>Аргументы

* `date`: `datetime`
* `part`: `string`

Возможные значения `part` : 
- Year
- Quarter
- Month
- week_of_year
- День
- DayOfYear
- Hour
- Минута
- Second
- Миллисекунда
- Микросекунды
- Потребовавшееся

## <a name="returns"></a>Результаты

Целое число, представляющее извлеченную часть.

**Примечание**

`week_of_year`Возвращает целое число, представляющее номер недели. Номер недели вычисляется с первой недели года, которая включает первый четверг.

## <a name="examples"></a>Примеры

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
> `weekofyear`является устаревшим вариантом `week_of_year` части. `weekofyear`не соответствует стандарту ISO 8601; Первая неделя года была определена как неделя с первой в году.
`week_of_year`соответствует стандарту ISO 8601; Первая неделя года определяется как неделя с первым четверг года в нем. [Дополнительные сведения](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).