---
title: startofyear () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается startofyear () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 973ea6d5043db0f173fa0ebe98548b20968371b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241161"
---
# <a name="startofyear"></a>startofyear()

Возвращает начало года, содержащего дату, смещенную по смещению, если оно предоставлено.

## <a name="syntax"></a>Синтаксис

`startofyear(`*Дата* [ `,` *смещение*]`)`

## <a name="arguments"></a>Аргументы

* `date`: Входная дата.
* `offset`: Необязательное число лет смещения от даты ввода (целое число, по умолчанию — 0). 

## <a name="returns"></a>Результаты

Значение типа DateTime, представляющее начало года для заданного значения *даты* , со смещением, если оно указано.

## <a name="example"></a>Пример

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|еарстарт|
|---|
|2016-01-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2018-01-01 00:00:00.0000000|