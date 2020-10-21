---
title: make_datetime () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_datetime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09a199e907f8e09a845fa037129330a2b660187e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249845"
---
# <a name="make_datetime"></a>make_datetime()

Создает скалярное значение [DateTime](./scalar-data-types/datetime.md) из указанной даты и времени.

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

## <a name="syntax"></a>Синтаксис

`make_datetime(`*год*,*месяц*,*день*`)`

`make_datetime(`*год*,*месяц*,*день*,*час*,*минута*`)`

`make_datetime(`*год*,*месяц*,*день*,*час*,*минута*,*секунда*`)`

## <a name="arguments"></a>Аргументы

* *year*— year (целое значение, от 0 до 9999)
* *месяц*: месяц (целочисленное значение от 1 до 12)
* *день*: день (целое значение от 1 до 28-31)
* *час*: час (целое значение, от 0 до 23)
* *минута*: минута (целочисленное значение от 0 до 59)
* *секунда*: секунда (вещественное значение, от 0 до 59,9999999)

## <a name="returns"></a>Результаты

Если создание прошло успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) , в противном случае Result будет иметь значение null.
 
## <a name="example"></a>Пример

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

