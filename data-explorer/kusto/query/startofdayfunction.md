---
title: startofday() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается начало дня () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6517b50ca880085761212ae9037cee96d20a3269
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507282"
---
# <a name="startofday"></a>startofday()

Возвращает начало дня, содержащего дату, сдвинутую на смещение, если это предусмотрено.

**Синтаксис**

`startofday(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество офсетных дней с даты ввода (целый ряд, по умолчанию - 0). 

**Возвращает**

Время даты, представляющее начало дня для данной *даты,* с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|дневной старт|
|---|
|2016-12-31 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-02 00:00:00.0000000|