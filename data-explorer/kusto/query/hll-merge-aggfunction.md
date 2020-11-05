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
ms.openlocfilehash: 59813656fce6afea3ecba62b13c971e74b095fe1
ms.sourcegitcommit: 42cc7d11f41a5bfa9e021764b044dcd68d99a258
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93403720"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (агрегатная функция)

Объединяет `HLL` результаты по группе в одно `HLL` значение.

* Может использоваться только в контексте статистической обработки внутри [итоговых](summarizeoperator.md)данных.

Дополнительные сведения см. в разделе [базовый алгоритм ( *H* ИПЕР *l* OG *l* OG) и точность оценки](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Синтаксис

`summarize``hll_merge(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* `*Expr*`: Выражение, которое будет использоваться для вычисления статистической обработки.

## <a name="returns"></a>Возвращаемое значение

Функция возвращает объединенные `hll` значения `*Expr*` по всей группе.
 
**Советы**

1) Используйте [dcount_hll](dcount-hllfunction.md) функции для вычисления `dcount` `hll`  /  `hll-merge` статистических функций from.
