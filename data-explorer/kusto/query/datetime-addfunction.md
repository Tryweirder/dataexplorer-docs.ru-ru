---
title: datetime_add () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается datetime_add () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ab395dadf178b296929300fe4cfd42742fba5f27
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247735"
---
# <a name="datetime_add"></a>datetime_add()

Вычисляет новое [значение DateTime](./scalar-data-types/datetime.md) из указанной части, умноженной на указанный объем, добавленную к указанному [значению DateTime](./scalar-data-types/datetime.md).

## <a name="syntax"></a>Синтаксис

`datetime_add(`*период времени* `,` *Сумма* `,` *Дата и время*`)`

## <a name="arguments"></a>Аргументы

* `period`: [String](./scalar-data-types/string.md). 
* `amount`: [целое число](./scalar-data-types/int.md).
* `datetime`: значение [DateTime](./scalar-data-types/datetime.md) .

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

Была добавлена дата после определенного интервала времени или даты.

## <a name="examples"></a>Примеры

```kusto
print  year = datetime_add('year',1,make_datetime(2017,1,1)),
quarter = datetime_add('quarter',1,make_datetime(2017,1,1)),
month = datetime_add('month',1,make_datetime(2017,1,1)),
week = datetime_add('week',1,make_datetime(2017,1,1)),
day = datetime_add('day',1,make_datetime(2017,1,1)),
hour = datetime_add('hour',1,make_datetime(2017,1,1)),
minute = datetime_add('minute',1,make_datetime(2017,1,1)),
second = datetime_add('second',1,make_datetime(2017,1,1))

```

|year|квартал|month|week|day|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






