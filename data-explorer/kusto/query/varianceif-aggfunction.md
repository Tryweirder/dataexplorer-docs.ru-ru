---
title: варианцеиф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается варианцеиф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf1009d2d269bf21ea5ae14a9c828724d8bf8c70
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338479"
---
# <a name="varianceif-aggregation-function"></a>варианцеиф () (агрегатная функция)

Вычисляет [дисперсию](variance-aggfunction.md) *выражения* в группе, для которой вычисляется *предикат* `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `varianceif(` *Expr* `, ` *предикат* выражения`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 
* *Предикат*: predicate, если true, вычисляемое значение *expr* будет добавлено к дисперсии.

## <a name="returns"></a>Результаты

Значение дисперсии *выражения* для *предиката* WHERE Group равно `true` .
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|