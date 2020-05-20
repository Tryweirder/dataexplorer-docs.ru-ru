---
title: тоинт () — Azure обозреватель данных
description: В этой статье описывается тоинт () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7f0ede908be2689165f641038b2b6f699c0eb543
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550611"
---
# <a name="toint"></a>toint()

Преобразует входные данные в целочисленное (со знаком 32-разрядное) представление числа.

```kusto
toint("123") == 123s
```

**Синтаксис**

`toint(`*Выражения*`)`

**Аргументы**

* *Expr*: выражение, которое будет преобразовано в тип Integer. 

**Возвращает**

Если преобразование прошло успешно, результатом будет целое число.
Если преобразование завершилось неудачно, результатом будет `null` .
 
*Примечание*. предпочитать использование [int ()](./scalar-data-types/int.md) , если это возможно.