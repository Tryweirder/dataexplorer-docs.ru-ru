---
title: hash () — Azure обозреватель данных
description: В этой статье описывается хэш () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0a8f1b2a72ceb154b7252b3218718a9c23968cb
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763869"
---
# <a name="hash"></a>hash()

Возвращает хэш-значение для входного значения.

**Синтаксис**

`hash(`*источник* [ `,` *Mod*]`)`

**Аргументы**

* *Source*: значение, которое необходимо хэшировать.
* *Mod*: необязательное значение модуля, применяемое к результирующему значению хэша, чтобы выходное значение было между `0` и *Mod* -1

**Возвращает**

Хэш-значение данного скаляра, остаток от деления заданного значения Mod (если указано).

> [!WARNING]
> Алгоритм, используемый для вычисления хэша, — ксксхаш.
> Этот алгоритм может измениться в будущем, и единственной гарантией является то, что внутри одного запроса все вызовы этого метода используют один и тот же алгоритм.
> Поэтому рекомендуется не хранить результаты `hash()` в таблице. Если требуется сохранить хэш-значения, используйте вместо него [hash_sha256 ()](./sha256hashfunction.md) или [hash_md5 ()](./md5hashfunction.md) . Обратите внимание, что эти функции более сложны для вычисления, чем `hash()` ).

**Примеры**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

В следующем примере хэш-функция используется для выполнения запроса на 10% данных, поэтому полезно использовать хэш-функцию для выборки данных при условии, что значение распределяется равномерно (в этом примере это значение StartTime).

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
