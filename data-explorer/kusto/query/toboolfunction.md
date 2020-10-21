---
title: Тобул () — Azure обозреватель данных
description: В этой статье описывается Тобул () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7fa3d12b1dfc1fcbede2f5f47b3841102b72e5a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250590"
---
# <a name="tobool"></a>tobool()

Преобразует входные данные в логическое представление (8-разрядное значение со знаком).

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>Синтаксис

`tobool(`*Expr* `)` 
 Выражение `toboolean(` *Выражение* `)` псевдоним

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет преобразовано в тип Boolean. 

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет логическое значение.
Если преобразование не выполнено, результатом будет `null` .
