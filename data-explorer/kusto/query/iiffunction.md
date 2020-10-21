---
title: IIf () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается IIf () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0279e3b0bfc28397b2270f012b8152b456c770cd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242416"
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

## <a name="returns"></a>Результаты

Эта функция возвращает значение *ifTrue*, если *predicate* принимает значение `true`. В противном случае она возвращает значение *ifFalse*.

## <a name="example"></a>Пример

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

Псевдоним для [`iff()`](ifffunction.md) .