---
title: hash () — Azure обозреватель данных
description: В этой статье описывается хэш () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d840ba106079a85435ee88f8b73cfc6daa7e4c5b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252322"
---
# <a name="hash"></a>hash()

Возвращает хэш-значение для входного значения.

## <a name="syntax"></a>Синтаксис

`hash(`*источник* [ `,` *Mod*]`)`

## <a name="arguments"></a>Аргументы

* *Source*: значение, которое необходимо хэшировать.
* *Mod*: необязательное значение модуля, применяемое к результирующему значению хэша, чтобы выходное значение было между `0` и *Mod* -1

## <a name="returns"></a>Результаты

Хэш-значение данного скаляра, остаток от деления заданного значения Mod (если указано).

> [!WARNING]
> Алгоритм, используемый для вычисления хэша, — ксксхаш.
> Этот алгоритм может измениться в будущем, и единственной гарантией является то, что внутри одного запроса все вызовы этого метода используют один и тот же алгоритм.
> Поэтому рекомендуется не хранить результаты `hash()` в таблице. Если требуется сохранить хэш-значения, используйте вместо него [hash_sha256 ()](./sha256hashfunction.md) или [hash_md5 ()](./md5hashfunction.md) . Обратите внимание, что эти функции более сложны для вычисления, чем `hash()` ).

## <a name="examples"></a>Примеры

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
