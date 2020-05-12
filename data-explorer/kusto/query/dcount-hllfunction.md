---
title: dcount_hll () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается dcount_hll () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: d4a76a30526f5fecbafafd735cf72de92aae7644
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225196"
---
# <a name="dcount_hll"></a>dcount_hll()

Вычисляет DCount из результатов ХЛЛ (которые были созданы [ХЛЛ](hll-aggfunction.md) или [hll_merge](hll-merge-aggfunction.md)).

Прочтите сведения о [базовом алгоритме (*H*ИПЕР*l*OG*l*OG) и точности оценки](dcount-aggfunction.md#estimation-accuracy).

**Синтаксис**

`dcount_hll(`*Выражения*`)`

**Аргументы**

* *Expr*: выражение, созданное с помощью [ХЛЛ](hll-aggfunction.md) или [ХЛЛ-Merge](hll-merge-aggfunction.md)

**Возвращает**

Число различных значений каждого значения в *expr*

**Примеры**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|