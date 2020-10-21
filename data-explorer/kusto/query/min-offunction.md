---
title: min_of () — обозреватель данных Azure
description: В этой статье описывается min_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c08ef6f147640330cd5ea33c55dcc6acafd77a31
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248816"
---
# <a name="min_of"></a>min_of()

Возвращает минимальное значение нескольких вычисленных числовых выражений.

```kusto
min_of(10, 1, -3, 17) == -3
```

## <a name="syntax"></a>Синтаксис

`min_of``(` *expr_1* `,` *expr_2* ...`)`

## <a name="arguments"></a>Аргументы

* *expr_i*— скалярное выражение для вычисления.

- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.

## <a name="returns"></a>Результаты

Минимальное значение всех выражений аргументов.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|набор по|
|---|
|–3|
