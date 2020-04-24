---
title: min_of() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны min_of () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06a8f7ce6bcef8f3c15c4ea3d4c997b4e4540bf7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512399"
---
# <a name="min_of"></a>min_of()

Возвращает минимальное значение нескольких оцененных численных выражений.

```kusto
min_of(10, 1, -3, 17) == -3
```

**Синтаксис**

`min_of``(` *expr_1* expr_1`,` *expr_2* ...`)`

**Аргументы**

* *expr_i*: Выражаемый выражение, который будет оценен.

- Все аргументы должны быть одного типа.
- Поддерживается максимум 64 аргумента.

**Возвращает**

Минимальное значение всех выражений аргументов.

**Пример**

```kusto
print result=min_of(10, 1, -3, 17) 
```

|набор по|
|---|
|–3|