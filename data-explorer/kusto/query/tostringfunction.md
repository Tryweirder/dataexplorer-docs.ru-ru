---
title: ToString () — Azure обозреватель данных
description: В этой статье описывается ToString () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 43797dcaa5e1a18b6a84f15fc0af89d88d814ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243799"
---
# <a name="tostring"></a>tostring()

Преобразует входные данные в строковое представление.

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>Синтаксис

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Выражение, которое будет преобразовано в строку. 

## <a name="returns"></a>Результаты

Если *`Expr`* значение не равно null, результатом будет строковое представление *`Expr`* .
Если *`Expr`* значение равно null, результатом будет пустая строка.
 