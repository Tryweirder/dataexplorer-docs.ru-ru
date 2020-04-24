---
title: totimespan () - Azure Data Explorer Документы Майкрософт
description: В этой статье описанto totimespan () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 504d4a74e8c1b58a8a97fd80d6c846fcf7e3f527
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505871"
---
# <a name="totimespan"></a>totimespan()

Преобразует входные в скальные [временные](./scalar-data-types/timespan.md) промежутки.

```kusto
totimespan("0.00:01:00") == time(1min)
```

**Синтаксис**

`totimespan(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в [timespan](./scalar-data-types/timespan.md). 

**Возвращает**

Если преобразование успешно, результат будет значение [промежуток времени.](./scalar-data-types/timespan.md)
Если конверсия не будет успешной, результат будет нулевым.
 