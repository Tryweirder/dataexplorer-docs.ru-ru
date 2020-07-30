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
ms.openlocfilehash: ecf16a714b0466a7ffc2da7b69f117383c90970e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347533"
---
# <a name="hll_merge"></a>hll_merge()

Объединяет `hll` результаты (скалярная версия агрегатной версии [`hll_merge()`](hll-merge-aggfunction.md) ).

Прочтите сведения о [базовом алгоритме (*H*ИПЕР*l*OG*l*OG) и точности оценки](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Синтаксис

`hll_merge(`*Выражение1* `,` *Выражение2*`, ...)`

## <a name="arguments"></a>Аргументы

* Столбцы, значения которых должны `hll` быть объединены.

## <a name="returns"></a>Результаты

Результат объединения столбцов `*Exrp1*` , `*Expr2*` ... `*ExprN*` в одно `hll` значение.

## <a name="examples"></a>Примеры

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
