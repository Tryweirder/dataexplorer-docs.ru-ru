---
title: startofweek () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается startofweek () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5f53dfca4183c4dae623b41abf4c5bce6a3e828
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241190"
---
# <a name="startofweek"></a>startofweek()

Возвращает начало недели, содержащей дату, смещенную по смещению, если оно предоставлено.

Начало недели считается воскресеньем.

## <a name="syntax"></a>Синтаксис

`startofweek(`*Дата* [ `,` *смещение*]`)`

## <a name="arguments"></a>Аргументы

* `date`: Входная дата.
* `offset`: Необязательное количество смещенных недель с даты ввода (целое число, по умолчанию — 0).

## <a name="returns"></a>Результаты

Значение типа DateTime, представляющее начало недели для заданного значения *даты* со смещением, если оно указано.

## <a name="example"></a>Пример

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|викстарт|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|