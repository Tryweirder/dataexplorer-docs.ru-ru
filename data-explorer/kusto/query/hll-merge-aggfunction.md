---
title: hll_merge () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается hll_merge () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a6aa19e3a88e7338494d6f6fad0e23a5ab4a03e4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252307"
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
