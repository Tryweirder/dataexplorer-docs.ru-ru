---
title: format_bytes () — обозреватель данных Azure
description: В этой статье описывается format_bytes () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4da07433be5a052d71740931d4dedd9df0399f56
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227374"
---
# <a name="format_bytes"></a>format_bytes()

Форматирует число в виде строки, представляющей размер данных в байтах.

```kusto
format_bytes(1024) == '1 KB'"
```

**Синтаксис**

`format_bytes(`*значение* [ `,` *точность* [ `,` *единицы*]]`)`

**Аргументы**

* `value`: число, которое форматируется как размер данных в байтах.
* `precision`: (необязательно) число разрядов, до которого будет округляться значение. (значение по умолчанию — 0).
* `units`: (необязательно) единицы целевого размера данных, которые будут использоваться форматированием строки ( `Bytes` , `KB` ,,, `MB` `GB` `TB` , `PB` ). Если параметр пуст, единицы будут выбраны на основе входного значения.

**Возвращает**

Строка с результатом форматирования.

**Примеры**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print 
v1 = format_bytes(564),
v2 = format_bytes(10332, 1),
v3 = format_bytes(20010332),
v4 = format_bytes(20010332, 2),
v5 = format_bytes(20010332, 0, "KB")
```

|Версия 1|Версия 2|Версия 3|версия 4|V5|
|---|---|---|---|---|
|564 байт|10,1 КБ|19 МБ|19,08 МБ|19541 КБ|
