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
ms.openlocfilehash: f19883335149603fe7fc7429dabb62f565a0b402
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347992"
---
# <a name="format_bytes"></a>format_bytes()

Форматирует число в виде строки, представляющей размер данных в байтах.

```kusto
format_bytes(1024) == '1 KB'"
```

## <a name="syntax"></a>Синтаксис

`format_bytes(`*значение* [ `,` *точность* [ `,` *единицы*]]`)`

## <a name="arguments"></a>Аргументы

* `value`: число, которое форматируется как размер данных в байтах.
* `precision`: (необязательно) число разрядов, до которого будет округляться значение. (значение по умолчанию — 0).
* `units`: (необязательно) единицы целевого размера данных, которые будут использоваться форматированием строки ( `Bytes` , `KB` ,,, `MB` `GB` `TB` , `PB` ). Если параметр пуст, единицы будут выбраны на основе входного значения.

## <a name="returns"></a>Возвращаемое значение

Строка с результатом форматирования.

## <a name="examples"></a>Примеры

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
