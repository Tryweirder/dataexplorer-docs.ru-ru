---
title: hash_sha256 () — обозреватель данных Azure
description: В этой статье описывается hash_sha256 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32fa2f3ffefdbf1f14ed87e8e89444de322408c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372319"
---
# <a name="hash_sha256"></a>hash_sha256()

Возвращает значение хэша SHA256 для входного значения.

**Синтаксис**

`hash_sha256(`*источника*`)`

**Аргументы**

* *Source*: значение, которое необходимо хэшировать.

**Возвращает**

Значение хэша SHA256 данного скаляра, закодированное как шестнадцатеричная строка (строка символов, каждая из которых представляет одно шестнадцатеричное число от 0 до 255).

> [!WARNING]
> Алгоритм, используемый этой функцией (SHA256), гарантированно не изменяется в будущем, но он очень сложен для вычисления. Пользователям, которым требуется "упрощенная" хэш-функция в течение одного запроса, рекомендуется использовать функцию [Hash ()](./hashfunction.md) .

**Примеры**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

В следующем примере функция hash_sha256 используется для выполнения запроса к столбцу StartTime данных.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
