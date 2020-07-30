---
title: IIf () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается IIf () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d912f94a224b073fe9214f70077067d3a24c906
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347482"
---
# <a name="iif"></a>iif()

Вычисляет первый аргумент (предикат) и возвращает значение второго или третьего аргумента в зависимости от того, вычисляется ли предикат как `true` (Second) или `false` (третий).

Второй и третий аргументы должны быть одного типа.

## <a name="syntax"></a>Синтаксис

`iif(`*предикат* `,` *IfTrue* `,` *IfFalse*`)`

## <a name="arguments"></a>Аргументы

* *predicate*— выражение, результатом вычисления которого является `boolean` значение.
* *IfTrue*: выражение, которое вычисляется, и его значение, возвращенное из функции, если *предикат* возвращает `true` .
* *IfFalse*: выражение, которое вычисляется, и его значение, возвращенное из функции, если *предикат* возвращает `false` .

## <a name="returns"></a>Возвращаемое значение

Эта функция возвращает значение *ifTrue*, если *predicate* принимает значение `true`. В противном случае она возвращает значение *ifFalse*.

## <a name="example"></a>Пример

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

Псевдоним для [`iff()`](ifffunction.md) .