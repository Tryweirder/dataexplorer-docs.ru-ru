---
title: ХЛЛ () (агрегатная функция) — Azure обозреватель данных
description: В этой статье описывается ХЛЛ () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 4474299c804e1b54d3060d639d171652e770d989
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241614"
---
# <a name="hll-aggregation-function"></a>ХЛЛ () (агрегатная функция)

Вычисляет промежуточные результаты [`dcount`](dcount-aggfunction.md) по группе, только в контексте статистической обработки внутри [сводки](summarizeoperator.md).

Прочтите сведения о [базовом алгоритме (*H*ИПЕР*l*OG*l*OG) и точности оценки](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Синтаксис

`summarize hll(`*`Expr`* `[,` *`Accuracy`*`])`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Выражение, которое будет использоваться для вычисления статистической обработки. 
* *`Accuracy`*, если он указан, управляет балансом между скоростью и точностью.

  |Значение точности |Точность  |Speed  |Error  |
  |---------|---------|---------|---------|
  |`0` | lowest | самым | 1,6% |
  |`1` | default  | распределяют | 0,8% |
  |`2` | high | медленный | 0,4%  |
  |`3` | high | медленный | 0,28% |
  |`4` | очень высокий | самое | 0,2% |
    
## <a name="returns"></a>Результаты

Промежуточные результаты числа различных объектов *`Expr`* в группе.
 
**Советы**

1. Статистическую функцию можно использовать [`hll_merge`](hll-merge-aggfunction.md) для слияния более чем одного `hll` промежуточного результата (он работает `hll` только на выходе).

1. Вы можете использовать функцию [`dcount_hll`](dcount-hllfunction.md) , которая будет вычислять `dcount` `hll`  /  `hll_merge` статистические функции from.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|`hll_DamageProperty`|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024, 14], [-5473486921211236216,-6230876016761372746, 3953448761157777955, 4246796580750024372], []]|
|2007-09-20 21:50:00.0000000|[[1024, 14], [4835649640695509390], []]|
|2007-09-29 08:10:00.0000000|[[1024, 14], [4246796580750024372], []]|
|2007-12-30 16:00:00.0000000|[[1024, 14], [4246796580750024372,-8936707700542868125], []]|
