---
title: максиф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается максиф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4df30f50d82e0ad5af87acaaa88b55f151a2a77a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251707"
---
# <a name="maxif-aggregation-function"></a>максиф () (агрегатная функция)

Возвращает максимальное значение в группе, для которой вычисляется *предикат* `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

См. также функция- [Max ()](max-aggfunction.md) , которая возвращает максимальное значение в группе без выражения предиката.

## <a name="syntax"></a>Синтаксис

`summarize``maxif(` *Expr*, `,` *предикат*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 
* *Предикат*: predicate, если true, вычисляемое значение *expr* будет проверяться для максимального значения.

## <a name="returns"></a>Результаты

Максимальное значение *выражения expr* в группе, для которой вычисляется *предикат* `true` .

## <a name="examples"></a>Примеры

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|