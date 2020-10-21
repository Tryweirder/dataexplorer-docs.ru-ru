---
title: tdigest_merge () — обозреватель данных Azure
description: В этой статье описывается tdigest_merge () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 4b7c143d29b8a2f446f4929098e9fac4e6166796
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250627"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Объединяет `tdigest` результаты (скалярная версия агрегатной версии [`tdigest_merge()`](tdigest-merge-aggfunction.md) ).

Дополнительные сведения о базовом алгоритме (T-Digest) и предполагаемой ошибке см. [здесь](percentiles-aggfunction.md#estimation-error-in-percentiles).

## <a name="syntax"></a>Синтаксис

`merge_tdigests(`*Выражение1* `,` *Выражение2*`, ...)`

`tdigest_merge(`*Выражение1* `,` *Выражение2* `, ...)` — Псевдоним.

## <a name="arguments"></a>Аргументы

* Столбцы, имеющие `tdigest` Объединяемые значения.

## <a name="returns"></a>Результаты

Результат объединения столбцов `*Expr1*` , `*Expr2*` ... `*ExprN*` в один `tdigest` .

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize tdigestX = tdigest(x), tdigestY = tdigest(y)
| project merged = tdigest_merge(tdigestX, tdigestY)
| project percentile_tdigest(merged, 100, typeof(long))
```

|percentile_tdigest_merged|
|---|
|20|
