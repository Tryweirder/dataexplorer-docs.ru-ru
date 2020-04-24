---
title: endofweek() - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается конец недели () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83c080c60e34dbfdf19f7dde870621e34ded836d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515833"
---
# <a name="endofweek"></a>endofweek()

Возвращает конец недели, содержащую дату, сдвинутую на смещение, если это предусмотрено.

Последний день недели считается субботой.

**Синтаксис**

`endofweek(`*Дата* `,`и*смещение*`)`

**Аргументы**

* `date`: Дата ввода.
* `offset`: Дополнительное количество офсетных недель с даты ввода (целый ряд, по умолчанию - 0).

**Возвращает**

Время даты, представляющее конец недели для данной *даты* значения, с смещением, если указано.

**Пример**

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|Выходные|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-07 23:59:59.9999999|
|2017-01-14 23:59:59.9999999|