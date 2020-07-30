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
ms.openlocfilehash: 1edc5e3ef8c3c2dea65d332e6ceace653cc5c812
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340145"
---
# <a name="totimespan"></a>totimespan()

Преобразует входные данные в скалярную величину [TimeSpan](./scalar-data-types/timespan.md) .

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>Синтаксис

`totimespan(Expr)`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Выражение, которое будет преобразовано в [TimeSpan](./scalar-data-types/timespan.md).

## <a name="returns"></a>Возвращаемое значение

Если преобразование выполнено успешно, результатом будет значение [TimeSpan](./scalar-data-types/timespan.md) .
В противном случае Result будет иметь значение null.
