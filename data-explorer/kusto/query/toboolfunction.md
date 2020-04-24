---
title: tobool() - Лазурный исследователь данных (ru) Документы Майкрософт
description: Эта статья описывает tobool () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3e1867c99ae241b3f7e09ab8ee873d5ae5d374e0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506364"
---
# <a name="tobool"></a>tobool()

Преобразует входные данные в boolean (подписанное 8-битное) представление.

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**Синтаксис**

`tobool(`*Expr*`)`
`)` Expr (псевдоним)*Expr* `toboolean(`

**Аргументы**

* *Expr*: Выражение, которое будет преобразовано в boolean. 

**Возвращает**

Если преобразование успешно, результат будет boolean.
Если конверсия не будет `null`успешной, результат будет .
 