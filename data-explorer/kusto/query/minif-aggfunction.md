---
title: миниф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается миниф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6932a50d59ee3df73857bfd4230faaa2e10dd2b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251005"
---
# <a name="minif-aggregation-function"></a>миниф () (агрегатная функция)

Возвращает минимальное значение в группе, для которой вычисляется *предикат* `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

См. также функция [min ()](min-aggfunction.md) , которая возвращает минимальное значение в группе без выражения предиката.

## <a name="syntax"></a>Синтаксис

`summarize``minif(` *Expr*, `,` *предикат*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.
* *Предикат*: predicate, если true, вычисляемое значение *expr* будет проверяться как минимум.

## <a name="returns"></a>Результаты

Минимальное значение *выражения* в группе, для которого вычисляется *предикат* `true` .

## <a name="examples"></a>Примеры

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|