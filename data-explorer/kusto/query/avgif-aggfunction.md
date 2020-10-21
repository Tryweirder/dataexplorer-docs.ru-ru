---
title: авгиф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается авгиф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e0d112554ff77cb9c591f787bbb62f9a92e3b19d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248017"
---
# <a name="avgif-aggregation-function"></a>авгиф () (агрегатная функция)

Вычисляет [среднее значение](avg-aggfunction.md) *выражения* в группе, для которого вычисляется *предикат* `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `avgif(` *Expr* `, ` *предикат* выражения`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. Записи со `null` значениями игнорируются и не включаются в вычисление.
* *Предикат*: predicate, если true, вычисляемое значение *expr* будет добавлено к среднему значению.

## <a name="returns"></a>Результаты

Среднее значение *выражения* в *предикате* Group WHERE, равное `true` .
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|