---
title: тотимеспан () — Azure обозреватель данных
description: В этой статье описывается тотимеспан () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b785f346dd95a9c6a8cb9d6148e889c42ac4b02c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129045"
---
# <a name="totimespan"></a>totimespan()

Преобразует входные данные в скалярную величину [TimeSpan](./scalar-data-types/timespan.md) .

```kusto
totimespan("0.00:01:00") == time(1min)
```

**Синтаксис**

`totimespan(Expr)`

**Аргументы**

* *`Expr`*: Выражение, которое будет преобразовано в [TimeSpan](./scalar-data-types/timespan.md).

**Возвращает**

Если преобразование выполнено успешно, результатом будет значение [TimeSpan](./scalar-data-types/timespan.md) .
В противном случае Result будет иметь значение null.
