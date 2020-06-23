---
title: ToDateTime () — Azure обозреватель данных
description: В этой статье описывается ToDateTime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5c108b670534728f34db8975f16d713848dd8f4
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264614"
---
# <a name="todatetime"></a>todatetime()

Преобразует входные данные в скалярные значения [DateTime](./scalar-data-types/datetime.md) .

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**Синтаксис**

`todatetime(`*Выражения*`)`

**Аргументы**

* *Expr*: выражение, которое будет преобразовано в тип [DateTime](./scalar-data-types/datetime.md).

**Возвращает**

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) .
В противном случае результат будет равен null.
 
> [!NOTE]
> Предпочитать использование [DateTime ()](./scalar-data-types/datetime.md) , если это возможно.
