---
title: StartOfMonth () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается StartOfMonth () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7e13cf809174fd258f9e7245bb2e537e2408c28
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241739"
---
# <a name="startofmonth"></a>startofmonth()

Возвращает начало месяца, содержащего дату, смещенную по смещению, если оно предоставлено.

## <a name="syntax"></a>Синтаксис

`startofmonth(`*Дата* [ `,` *смещение*]`)`

## <a name="arguments"></a>Аргументы

* `date`: Входная дата.
* `offset`: Необязательное число месяцев смещения от даты ввода (целое число, по умолчанию — 0).

## <a name="returns"></a>Результаты

Значение типа DateTime, представляющее начало месяца для заданного значения *даты* со смещением, если оно указано.

## <a name="example"></a>Пример

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|монсстарт|
|---|
|2016-12-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-02-01 00:00:00.0000000|