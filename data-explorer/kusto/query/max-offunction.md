---
title: max_of () — обозреватель данных Azure
description: В этой статье описывается max_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b912b1bdc68d7b3071ace8547f0aaf7c679a86a
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271610"
---
# <a name="max_of"></a>max_of()

Возвращает максимальное значение нескольких вычисленных числовых выражений.

```kusto
max_of(10, 1, -3, 17) == 17
```

**Синтаксис**

`max_of``(` *expr_1* `,` *expr_2* ...`)`

**Аргументы**

* *expr_i*— скалярное выражение для вычисления.

- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.

**Возвращает**

Максимальное значение всех выражений аргументов.

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
