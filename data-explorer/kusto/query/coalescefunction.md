---
title: объединение () — Azure обозреватель данных
description: В этой статье описывается объединение () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea57efe36fb86189d798e5f18fa3fe9470bfd634
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227542"
---
# <a name="coalesce"></a>coalesce()

Вычисляет список выражений и возвращает первое выражение, отличное от NULL (или не пустое для строки).

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**Синтаксис**

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

**Аргументы**

* *expr_i*— скалярное выражение для вычисления.
- Все аргументы должны быть одного типа.
- Поддерживается не более 64 аргументов.


**Возвращает**

Значение первого *expr_i* , значение которого не равно null (или не является пустым для строковых выражений).

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
