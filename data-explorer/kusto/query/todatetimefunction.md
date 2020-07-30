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
ms.openlocfilehash: 6a991f9f64decbb3985830cf2611361d6c66db31
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350848"
---
# <a name="todatetime"></a>todatetime()

Преобразует входные данные в скалярные значения [DateTime](./scalar-data-types/datetime.md) .

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

## <a name="syntax"></a>Синтаксис

`todatetime(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в тип [DateTime](./scalar-data-types/datetime.md).

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет значение [DateTime](./scalar-data-types/datetime.md) .
В противном случае результат будет равен null.
 
> [!NOTE]
> Предпочитать использование [DateTime ()](./scalar-data-types/datetime.md) , если это возможно.
