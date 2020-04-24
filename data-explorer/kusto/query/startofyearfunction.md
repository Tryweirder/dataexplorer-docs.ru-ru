---
title: startofyear () - Azure Data Explorer Документы Майкрософт
description: Эта статья описывает startofyear() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f91c749cc3833954d902eb4ebd7e230e32e3a991
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507214"
---
# <a name="startofyear"></a>startofyear()

Возвращает начало года, содержащее дату, сдвинутую на смещение, если это предусмотрено.

**Синтаксис**

`startofyear(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество офсетных лет с даты ввода (целый ряд, по умолчанию - 0). 

**Возвращает**

Время дат, представляющее начало года для данной *даты,* с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|годНачало|
|---|
|2016-01-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2018-01-01 00:00:00.0000000|