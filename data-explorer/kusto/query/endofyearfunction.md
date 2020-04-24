---
title: endofyear() - Azure Data Explorer Документы Майкрософт
description: Эта статья описывает endofyear() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4a14d1cc42d5b9116e8a144e2b67fb74c1932ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515782"
---
# <a name="endofyear"></a>endofyear()

Возвращает конец года, содержащий дату, сдвинутую на смещение, если это предусмотрено.

**Синтаксис**

`endofyear(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество офсетных лет с даты ввода (целый ряд, по умолчанию - 0).

**Возвращает**

Время дат, представляющее конец года для данной *даты,* с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-12-31 23:59:59.9999999|
|2018-12-31 23:59:59.9999999|