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
ms.openlocfilehash: 4681f92155181f85cad5c46ed70a79cb173d437f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347550"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (агрегатная функция)

Объединяет `HLL` результаты по группе в одно `HLL` значение.

* Может использоваться только в контексте статистической обработки внутри [итоговых](summarizeoperator.md)данных.

Дополнительные сведения см. в разделе [базовый алгоритм (*H*ИПЕР*l*OG*l*OG) и точность оценки](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Синтаксис

`summarize``hll_merge(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* `*Expr*`: Выражение, которое будет использоваться для вычисления статистической обработки.

## <a name="returns"></a>Результаты

Функция возвращает объединенные `hll` значения `*Expr*` по всей группе.
 
**Советы**

1) Используйте функцию [dcount_hll] (DCount-hllfunction.md), чтобы вычислить `dcount` из `hll`  /  `hll-merge` агрегатных функций.
