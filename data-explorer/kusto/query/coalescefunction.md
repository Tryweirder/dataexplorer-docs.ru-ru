---
title: объединиться () - Azure Data Explorer Документы Майкрософт
description: В этой статье описано объединение () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ee2cd24f36007914fdc326e2863da148aec4406
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517142"
---
# <a name="coalesce"></a>coalesce()

Оценивает список выражений и возвращает первое ненулевое (или непустое для строки) выражение.

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**Синтаксис**

`coalesce(`*expr_1*`, `*expr_2* `,` ...)

**Аргументы**

* *expr_i*: Выражаемый выражение, который будет оценен.
- Все аргументы должны быть одного типа.
- Поддерживается максимум 64 аргумента.


**Возвращает**

Значение первого *expr_i* значение которого не является нулевым (или не пустым для выражений строки).

**Пример**

```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|набор по|
|---|
|42|