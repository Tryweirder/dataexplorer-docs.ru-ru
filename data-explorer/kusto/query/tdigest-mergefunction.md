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
ms.openlocfilehash: 1281e2afdf9770975c6f6f74399f9815adaec045
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371064"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Объединяет `tdigest` результаты (скалярная версия агрегатной версии [`tdigest_merge()`](tdigest-merge-aggfunction.md) ).

Дополнительные сведения о базовом алгоритме (T-Digest) и предполагаемой ошибке см. [здесь](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Синтаксис**

`merge_tdigests(`*Выражение1* `,` *Выражение2*`, ...)`

`tdigest_merge(`*Выражение1* `,` *Выражение2* `, ...)` — Псевдоним.

**Аргументы**

* Столбцы, имеющие `tdigest` Объединяемые значения.

**Возвращает**

Результат объединения столбцов `*Expr1*` , `*Expr2*` ... `*ExprN*` в один `tdigest` .

**Примеры**

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
