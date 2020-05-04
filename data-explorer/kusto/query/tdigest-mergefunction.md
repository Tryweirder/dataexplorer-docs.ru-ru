---
title: tdigest_merge () — обозреватель данных Azure
description: В этой статье описывается tdigest_merge () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 92dce1a98cc0e24dcfbfcd7cb875fa370e3ae1d0
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737731"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Объединяет `tdigest` результаты (скалярная версия агрегатной версии [`tdigest_merge()`](tdigest-merge-aggfunction.md)).

Дополнительные сведения о базовом алгоритме (T-Digest) и предполагаемой ошибке см. [здесь](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Синтаксис**

`merge_tdigests(`*Выражение1* `,` *выражение2*`, ...)`

`tdigest_merge(`*Выражение1* `,` *Expr2* выражение2`, ...)` — псевдоним.

**Аргументы**

* Столбцы, имеющие объединяемые `tdigest` значения.

**Возвращает**

Результат объединения столбцов `*Expr1*`, `*Expr2*`,... `*ExprN*` на один `tdigest`.

**Примеры**

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