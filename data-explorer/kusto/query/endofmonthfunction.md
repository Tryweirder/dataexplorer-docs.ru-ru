---
title: EndOfMonth () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается EndOfMonth () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1911546f95b86a04fe59d22137b1eaeff22e2c2a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245148"
---
# <a name="endofmonth"></a>endofmonth()

Возвращает конец месяца, содержащего дату, смещенную по смещению, если оно предоставлено.

## <a name="syntax"></a>Синтаксис

`endofmonth(`*Дата* [ `,` *смещение*]`)`

## <a name="arguments"></a>Аргументы

* `date`: Входная дата.
* `offset`: Необязательное число месяцев смещения от даты ввода (целое число, по умолчанию — 0).

## <a name="returns"></a>Результаты

Значение типа DateTime, представляющее конец месяца для заданного значения *даты* и смещение, если оно указано.

## <a name="example"></a>Пример

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|монсенд|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-31 23:59:59.9999999|
|2017-02-28 23:59:59.9999999|