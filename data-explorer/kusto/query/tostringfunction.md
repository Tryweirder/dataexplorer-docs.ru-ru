---
title: ToString () — Azure обозреватель данных
description: В этой статье описывается ToString () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 634f54533e83575139d8399124cc068af56d8574
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741670"
---
# <a name="tostring"></a>tostring()

Преобразует входные данные в строковое представление.

```kusto
tostring(123) == "123"
```

**Синтаксис**

`tostring(`*`Expr`*`)`

**Аргументы**

* *`Expr`*: Выражение, которое будет преобразовано в строку. 

**Возвращает**

Если *`Expr`* значение не равно null, результатом будет строковое представление *`Expr`*.
Если *`Expr`* значение равно null, результатом будет пустая строка.
 