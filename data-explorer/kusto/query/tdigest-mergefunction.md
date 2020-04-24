---
title: tdigest_merge() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается tdigest_merge () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 988d7f05791723a823a5850f6865a780477f7bd4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506381"
---
# <a name="tdigest_merge"></a>tdigest_merge()

Слияния tdigest результаты (масштабная версия [`tdigest_merge()`](tdigest-merge-aggfunction.md)агрегированной версии ).

Узнайте больше о базовом алгоритме (T-Digest) и предполагаемой ошибке [здесь](percentiles-aggfunction.md#estimation-error-in-percentiles).

**Синтаксис**

`merge_tdigests(`*Expr1* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` - Псевдоним.

**Аргументы**

* Столбцы, которые имеет tdigests должны быть объединены.

**Возвращает**

Результат для слияния столбцов `*Expr1*` `*Expr2*`, , ... `*ExprN*` до одного траварья.

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