---
title: min_of () — обозреватель данных Azure
description: В этой статье описывается min_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0762b1416df32279b9801c47f129a6966772a7e2
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271372"
---
# <a name="min_of"></a>min_of()

Возвращает минимальное значение нескольких вычисленных числовых выражений.

```kusto
min_of(10, 1, -3, 17) == -3
```

**Синтаксис**

`min_of``(` *expr_1* `,` *expr_2* ...`)`

**Аргументы**

* *expr_i*— скалярное выражение для вычисления.

- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.

**Возвращает**

Минимальное значение всех выражений аргументов.

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|–3|
