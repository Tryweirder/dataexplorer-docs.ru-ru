---
title: hll_merge () — обозреватель данных Azure
description: В этой статье описывается hll_merge () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 35dab83a658b714a220c7fbd6ff751627c0741e4
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741790"
---
# <a name="hll_merge"></a>hll_merge()

Объединяет `hll` результаты (скалярная версия агрегатной версии [`hll_merge()`](hll-merge-aggfunction.md)).

Прочтите сведения о [базовом алгоритме (*H*ИПЕР*l*OG*l*OG) и точности оценки](dcount-aggfunction.md#estimation-accuracy).

**Синтаксис**

`hll_merge(`*Выражение1* `,` *выражение2*`, ...)`

**Аргументы**

* Столбцы, `hll` значения которых должны быть объединены.

**Возвращает**

Результат объединения столбцов `*Exrp1*`, `*Expr2*`,... `*ExprN*` к одному `hll` значению.

**Примеры**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|`dcount_hll_merged`|
|---|
|20|