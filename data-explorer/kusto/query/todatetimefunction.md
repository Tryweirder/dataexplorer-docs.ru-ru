---
title: ToDateTime () — Azure обозреватель данных
description: В этой статье описывается ToDateTime () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b9248ddc76a4893cf1edd353c0fbd036c9a1e7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250531"
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
