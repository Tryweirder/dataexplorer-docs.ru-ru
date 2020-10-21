---
title: стдевиф () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается стдевиф () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 831011bcd72c2fc9b06c3c68c6ebda7929d49e4b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242528"
---
# <a name="stdevif-aggregation-function"></a>стдевиф () (агрегатная функция)

Вычисляет отклонение *выражения* [в группе](stdev-aggfunction.md) , для которой *предикат* возвращает значение `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `stdevif(` *Expr* `, ` *предикат* выражения`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 
* *Предикат*: predicate, если true, вычисляемое значение *expr* будет добавлено к стандартному отклонению.

## <a name="returns"></a>Результаты

Стандартное значение отклонения в *выражении* для *предиката* WHERE группы равно `true` .
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|