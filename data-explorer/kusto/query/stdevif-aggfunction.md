---
title: стдевиф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается стдевиф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a158a623768a7beb6ec497ca8d8467aecd7c3b61
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342824"
---
# <a name="stdevif-aggregation-function"></a>стдевиф () (агрегатная функция)

Вычисляет отклонение *выражения* [в группе](stdev-aggfunction.md) , для которой *предикат* возвращает значение `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `stdevif(` *Expr* `, ` *предикат* выражения`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 
* *Предикат*: predicate, если true, вычисляемое значение *expr* будет добавлено к стандартному отклонению.

## <a name="returns"></a>Возвращаемое значение

Стандартное значение отклонения в *выражении* для *предиката* WHERE группы равно `true` .
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|