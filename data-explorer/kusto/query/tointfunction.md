---
title: тоинт () — Azure обозреватель данных
description: В этой статье описывается тоинт () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1efd06b8fa2961528be65b630933aae74614e9a0
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013593"
---
# <a name="toint"></a>toint()

Преобразует входные данные в целочисленное (со знаком 32-разрядное) представление числа.

```kusto
toint("123") == int(123)
```

**Синтаксис**

`toint(`*Выражения*`)`

**Аргументы**

* *Expr*: выражение, которое будет преобразовано в тип Integer. 

**Возвращает**

Если преобразование прошло успешно, результатом будет целое число.
Если преобразование завершилось неудачно, результатом будет `null` .
 
*Примечание*. предпочитать использование [int ()](./scalar-data-types/int.md) , если это возможно.
