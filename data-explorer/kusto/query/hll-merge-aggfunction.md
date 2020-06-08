---
title: hll_merge () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается hll_merge () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 59c6f6a11b108cf6e74ceb59d3483ea1a95f7002
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512390"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (агрегатная функция)

Объединяет `HLL` результаты по группе в одно `HLL` значение.

* Может использоваться только в контексте статистической обработки внутри [итоговых](summarizeoperator.md)данных.

Дополнительные сведения см. в разделе [базовый алгоритм (*H*ИПЕР*l*OG*l*OG) и точность оценки](dcount-aggfunction.md#estimation-accuracy).

**Синтаксис**

`summarize``hll_merge(` *Выражение*`)`

**Аргументы**

* `*Expr*`: Выражение, которое будет использоваться для вычисления статистической обработки.

**Возвращает**

Функция возвращает объединенные `hll` значения `*Expr*` по всей группе.
 
**"Советы"**

1) Используйте функцию [dcount_hll] (DCount-hllfunction.md), чтобы вычислить `dcount` из `hll`  /  `hll-merge` агрегатных функций.
