---
title: Тобул () — Azure обозреватель данных
description: В этой статье описывается Тобул () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f99406d94e1cd64da8605e5000aa99136c2b119a
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763773"
---
# <a name="tobool"></a>tobool()

Преобразует входные данные в логическое представление (8-разрядное значение со знаком).

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**Синтаксис**

`tobool(`*Expr* `)` 
 Выражение `toboolean(` *Выражение* `)` псевдоним

**Аргументы**

* *Expr*: выражение, которое будет преобразовано в тип Boolean. 

**Возвращает**

Если преобразование выполнено успешно, результатом будет логическое значение.
Если преобразование не выполнено, результатом будет `null` .
