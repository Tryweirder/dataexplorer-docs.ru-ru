---
title: iff() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны iff() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5eb8af5fd98fb24c677390c314d112e5634cacf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514065"
---
# <a name="iff"></a>iff()

Оценивает первый аргумент (предикат) и возвращает значение второго или третьего аргумента, в зависимости от того, оценивается `true` ли предикат (второй) или `false` (третий).

Второй и третий аргументы должны быть одного типа.

**Синтаксис**

`iff(`*предикат,* `,` *еслиПравда* `,` *ifFalse*`)`

**Аргументы**

* *предикат*: Выражение, которое `boolean` оценивает значение.
* *еслиTrue*: Выражение, которое получает оценку и его значение `true`возвращается из функции, если *предикат* оценивает .
* *еслиFalse*: Выражение которое получает оцененным и свое значение возвращенное от функции если *predicate* оценивает к `false`.

**Возвращает**

Эта функция возвращает значение *ifTrue*, если *predicate* принимает значение `true`. В противном случае она возвращает значение *ifFalse*.

**Пример**

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```