---
title: startofweek() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описанstartofweek() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9705b586a0b8c5161b7d4c27735f644b6982c707
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507231"
---
# <a name="startofweek"></a>startofweek()

Возвращает начало недели, содержащее дату, сдвинутую на смещение, если это предусмотрено.

Начало недели считается воскресеньем.

**Синтаксис**

`startofweek(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество офсетных недель с даты ввода (целый ряд, по умолчанию - 0).

**Возвращает**

Время даты, представляющее начало недели для данной *даты,* с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|неделяНачало|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|