---
title: tdigest_merge () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается tdigest_merge () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 14b6b1c5ed31c312065e7641783bd1dc524993d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250642"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge () (агрегатная функция)

Слияние результатов тдижест в группе. 

* Может использоваться только в контексте статистической обработки внутри [итоговых](summarizeoperator.md)данных.

Дополнительные сведения о базовом алгоритме (T-Digest) и предполагаемой ошибке см. [здесь](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Синтаксис

суммировать `tdigest_merge(` *выражение* `)` .

суммировать `tdigest_merge(` *выражение* `)` — псевдоним.

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Объединенные значения тдижест типа *expr* в группе.
 

**Советы**

1) Вы можете использовать функцию [ `percentile_tdigest()` ] (percentile-tdigestfunction.md).

2) Все тдижестс, входящие в одну и ту же группу, должны иметь один и тот же тип.