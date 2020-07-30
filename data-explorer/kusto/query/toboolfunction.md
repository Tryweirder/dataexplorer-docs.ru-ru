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
ms.openlocfilehash: e0343ae5cb98e1cb3114e24c963fe2981be82c5b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350797"
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
