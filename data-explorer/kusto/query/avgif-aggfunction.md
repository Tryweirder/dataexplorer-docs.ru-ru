---
title: авгиф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается авгиф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 587af53de774332db70ef9bffcadf74d9e2c069d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349386"
---
# <a name="avgif-aggregation-function"></a>авгиф () (агрегатная функция)

Вычисляет [среднее значение](avg-aggfunction.md) *выражения* в группе, для которого вычисляется *предикат* `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `avgif(` *Expr* `, ` *предикат* выражения`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. Записи со `null` значениями игнорируются и не включаются в вычисление.
* *Предикат*: predicate, если true, вычисляемое значение *expr* будет добавлено к среднему значению.

## <a name="returns"></a>Возвращаемое значение

Среднее значение *выражения* в *предикате* Group WHERE, равное `true` .
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|