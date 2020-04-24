---
title: endofday () - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается конец дня () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a25f6b9c4ba660fbb8a50390d9d6920ff21caeb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515884"
---
# <a name="endofday"></a>endofday()

Возвращает конец дня, содержащий дату, сдвинутую на смещение, если это предусмотрено.

**Синтаксис**

`endofday(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество офсетных дней с даты ввода (целый ряд, по умолчанию - 0).

**Возвращает**

Время даты, представляющее конец дня для данной *даты,* с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project dayEnd = endofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayEnd|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-01 23:59:59.9999999|
|2017-01-02 23:59:59.9999999|