---
title: endofyear () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается endofyear () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a93b24af949beedc02ddd3dbbc479074f0aa891c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249135"
---
# <a name="endofyear"></a>endofyear()

Возвращает конец года, содержащего дату, смещенную по смещению, если оно предоставлено.

## <a name="syntax"></a>Синтаксис

`endofyear(`*Дата* [ `,` *смещение*]`)`

## <a name="arguments"></a>Аргументы

* `date`: Входная дата.
* `offset`: Необязательное число лет смещения от даты ввода (целое число, по умолчанию — 0).

## <a name="returns"></a>Результаты

Значение типа DateTime, представляющее конец года для заданного значения *даты* и смещение, если оно указано.

## <a name="example"></a>Пример

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|еаренд|
|---|
|2016-12-31 23:59:59.9999999|
|2017-12-31 23:59:59.9999999|
|2018-12-31 23:59:59.9999999|