---
title: стартофдай () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается стартофдай () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3b44da313c50360c73f63f1244ce2be959af2eb4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350950"
---
# <a name="startofday"></a>startofday()

Возвращает начало дня, содержащего дату, смещенную по смещению, если оно предоставлено.

## <a name="syntax"></a>Синтаксис

`startofday(`*Дата* [ `,` *смещение*]`)`

## <a name="arguments"></a>Аргументы

* `date`: Входная дата.
* `offset`: Необязательное число дней смещения от даты ввода (целое число, по умолчанию — 0). 

## <a name="returns"></a>Результаты

Значение типа DateTime, представляющее начало дня для заданного значения *даты* , со смещением, если оно указано.

## <a name="example"></a>Пример

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|дайстарт|
|---|
|2016-12-31 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-02 00:00:00.0000000|