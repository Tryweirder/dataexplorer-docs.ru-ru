---
title: hll_merge () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается hll_merge () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 10e726af4e9dd2e129b526f016a7c37dc0c99d50
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514099"
---
# <a name="hll_merge"></a>hll_merge()

Слияния hll результаты (масштабная версия [`hll_merge()`](hll-merge-aggfunction.md)агрегированной версии ).

Читайте о [базовом алгоритме (*H*yper*L*og*L*og) и точности оценки.](dcount-aggfunction.md#estimation-accuracy)

**Синтаксис**

`hll_merge(`*Expr1* `,` *Expr2*`, ...)`

**Аргументы**

* Столбцы, которые имеет значения hll, которые должны быть объединены.

**Возвращает**

Результат для слияния столбцов `*Exrp1*` `*Expr2*`, , ... `*ExprN*` до одного значения hll.

**Примеры**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|dcount_hll_merged|
|---|
|20|