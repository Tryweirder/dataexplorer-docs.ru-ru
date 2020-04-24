---
title: конец месяца () - Azure Data Explorer Документы Майкрософт
description: Эта статья описывает конец месяца () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebab067c730e3cd61c84ae33eba4e49d7f0b0c0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515867"
---
# <a name="endofmonth"></a>endofmonth()

Возвращает конец месяца, содержащего дату, сдвинутую на смещение, если это предусмотрено.

**Синтаксис**

`endofmonth(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество смещенных месяцев с даты ввода (целый ряд, по умолчанию - 0).

**Возвращает**

Время даты, представляющее конец месяца для данной *даты,* с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|месяцЭнд|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-31 23:59:59.9999999|
|2017-02-28 23:59:59.9999999|