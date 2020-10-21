---
title: dcount_hll () — обозреватель данных Azure
description: В этой статье описывается dcount_hll () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 743f35b6bf6d461c1d08c3082bb235b88b57ada2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252370"
---
# <a name="dcount_hll"></a>dcount_hll()

Вычисляет DCount из результатов ХЛЛ (созданных с помощью [ХЛЛ](hll-aggfunction.md) или [hll_merge](hll-merge-aggfunction.md)).

Прочтите сведения о [базовом алгоритме (*H*ИПЕР*l*OG*l*OG) и точности оценки](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Синтаксис

`dcount_hll(`*Выражения*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, созданное с помощью [ХЛЛ](hll-aggfunction.md) или [ХЛЛ-Merge](hll-merge-aggfunction.md)

## <a name="returns"></a>Результаты

Число различных значений каждого значения в *expr*

## <a name="examples"></a>Примеры

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
