---
title: make_datetime() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается make_datetime () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c51b99e4d668ec4a5f96cdfd72442d7bcab553a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512926"
---
# <a name="make_datetime"></a>make_datetime()

Создает значение времени [времени времени](./scalar-data-types/datetime.md) масштабирования из указанной даты и времени.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

**Синтаксис**

`make_datetime(`*год,**месяц,**день*`)`

`make_datetime(`*год,**месяц,**день,**час,**минута*`)`

`make_datetime(`*год,**месяц,**день,**час,**минута,**второй*`)`

**Аргументы**

* *год*: год (величина значение, от 0 до 9999)
* *месяц*: месяц (величина значение, от 1 до 12)
* *день*: день (величина значение, от 1 до 28-31)
* *час*: час (значение всей теплы, от 0 до 23)
* *минута*: минута (значение всей величины, от 0 до 59)
* *второй*: второй (реальное значение, от 0 до 59.9999999)

**Возвращает**

Если создание успешно, результат будет значение [даты,](./scalar-data-types/datetime.md) в противном случае, результат будет нулевым.
 
**Пример**

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00:00:00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12:10:00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12:11:00.1234567|

