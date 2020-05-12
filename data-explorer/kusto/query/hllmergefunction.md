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
ms.openlocfilehash: 2cddd645b0b89b3356adabae26874f93b41f8815
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226556"
---
# <a name="hll_merge"></a>hll_merge()

Объединяет `hll` результаты (скалярная версия агрегатной версии [`hll_merge()`](hll-merge-aggfunction.md) ).

Прочтите сведения о [базовом алгоритме (*H*ИПЕР*l*OG*l*OG) и точности оценки](dcount-aggfunction.md#estimation-accuracy).

**Синтаксис**

`hll_merge(`*Выражение1* `,` *Выражение2*`, ...)`

**Аргументы**

* Столбцы, значения которых должны `hll` быть объединены.

**Возвращает**

Результат объединения столбцов `*Exrp1*` , `*Expr2*` ... `*ExprN*` в одно `hll` значение.

**Примеры**

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
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
