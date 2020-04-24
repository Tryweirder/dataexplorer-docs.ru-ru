---
title: max_of () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны max_of() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68188ccd5eb814a22be166b8847d80193172813f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512501"
---
# <a name="max_of"></a>max_of()

Возвращает максимальное значение нескольких оцененных численных выражений.

```kusto
max_of(10, 1, -3, 17) == 17
```

**Синтаксис**

`max_of``(` *expr_1* expr_1`,` *expr_2* ...`)`

**Аргументы**

* *expr_i*: Выражаемый выражение, который будет оценен.

- Все аргументы должны быть одного типа.
- Поддерживается максимум 64 аргумента.

**Возвращает**

Максимальное значение всех выражений аргументов.

**Пример**

```kusto
print result = max_of(10, 1, -3, 17) 
```

|набор по|
|---|
|17|