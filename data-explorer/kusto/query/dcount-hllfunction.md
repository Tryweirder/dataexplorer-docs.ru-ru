---
title: dcount_hll () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны dcount_hll () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a0c921efa90f5d66fe42fa6ee872204b5bb399cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516156"
---
# <a name="dcount_hll"></a>dcount_hll()

Вычисляет количество от результатов hll (который был произведен [hll](hll-aggfunction.md) или [hll_merge).](hll-merge-aggfunction.md)

Читайте о [базовом алгоритме (*H*yper*L*og*L*og) и точности оценки.](dcount-aggfunction.md#estimation-accuracy)

**Синтаксис**

`dcount_hll(`*Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое было сгенерировано [hll](hll-aggfunction.md) или [hll-merge](hll-merge-aggfunction.md)

**Возвращает**

Четкое количество каждого значения в *Expr*

**Примеры**

```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|