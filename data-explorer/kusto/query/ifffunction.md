---
title: Одиночная () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается одиночная () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7eeab87f3c3ef42d1e00bf0d6b8853fe3a2f3125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347499"
---
# <a name="iff"></a>iff()

Вычисляет первый аргумент (предикат) и возвращает значение второго или третьего аргумента в зависимости от того, вычисляется ли предикат как `true` (Second) или `false` (третий).

Второй и третий аргументы должны быть одного типа.

## <a name="syntax"></a>Синтаксис

`iff(`*предикат* `,` *IfTrue* `,` *IfFalse*`)`

## <a name="arguments"></a>Аргументы

* *predicate*— выражение, результатом вычисления которого является `boolean` значение.
* *IfTrue*: выражение, которое вычисляется, и его значение, возвращенное из функции, если *предикат* возвращает `true` .
* *IfFalse*: выражение, которое вычисляется, и его значение, возвращенное из функции, если *предикат* возвращает `false` .

## <a name="returns"></a>Возвращаемое значение

Эта функция возвращает значение *ifTrue*, если *predicate* принимает значение `true`. В противном случае она возвращает значение *ifFalse*.

## <a name="example"></a>Пример

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```