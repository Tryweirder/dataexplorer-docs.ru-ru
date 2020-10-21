---
title: тотимеспан () — Azure обозреватель данных
description: В этой статье описывается тотимеспан () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0779a6260cc87f8a602f4751d28c33de9bacb57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243756"
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

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет значение [TimeSpan](./scalar-data-types/timespan.md) .
В противном случае Result будет иметь значение null.
