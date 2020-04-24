---
title: startofmonth() - Azure Data Explorer Документы Майкрософт
description: Эта статья описывает startofmonth() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e220919f6b09dbcd2519c67f48148a6375395e7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507248"
---
# <a name="startofmonth"></a>startofmonth()

Возвращает начало месяца, содержащего дату, сдвинутую на смещение, если это предусмотрено.

**Синтаксис**

`startofmonth(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество смещенных месяцев с даты ввода (целый ряд, по умолчанию - 0).

**Возвращает**

Время даты, представляющее начало месяца для данной *даты,* с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|месяцНачало|
|---|
|2016-12-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-02-01 00:00:00.0000000|