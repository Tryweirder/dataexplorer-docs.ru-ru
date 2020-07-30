---
title: максиф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается максиф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 471ca0e3d6623b77fd2d799949bfe060643798e2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346819"
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