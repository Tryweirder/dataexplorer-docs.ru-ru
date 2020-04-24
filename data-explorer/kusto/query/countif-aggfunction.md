---
title: countif() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны countif() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03b6f1cb959706463a73d8aa18a11144e2123492
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516989"
---
# <a name="countif-aggregation-function"></a>countif() (функция агрегирования)

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`.

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

Смотрите также - [кол-во ()](count-aggfunction.md) функция, которая считает строки без выражения предиката.

**Синтаксис**

резюмировать `countif(` *Предикат*`)`

**Аргументы**

* *Предикат*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Возвращает число строк, для которых предикат *Predicate* принимает значение `true`.

> [!TIP]
> Используйте `summarize countif(filter)` вместо `where filter | summarize count()`