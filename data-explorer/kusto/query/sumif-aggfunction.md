---
title: СУММЕСЛИ () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается процедура СУММЕСЛИ () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a62ff98810996aff1352b959768385bec76e8ba8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251341"
---
# <a name="sumif-aggregation-function"></a>СУММЕСЛИ () (агрегатная функция)

Возвращает сумму *выражения expr* , для которой *предикат* принимает значение `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

Можно также использовать функцию [Sum ()](sum-aggfunction.md) , которая суммирует строки без выражения предиката.

## <a name="syntax"></a>Синтаксис

суммировать `sumif(` *Expr* `,` *предикат* выражения`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение для вычисления агрегата. 
* *Предикат*: predicate, при значении true вычисляемое значение *expr*будет добавлено к сумме. 

## <a name="returns"></a>Результаты

Итоговое значение *выражения expr* , для *Predicate* которого вычисляется предикат `true` .

## <a name="example"></a>Пример

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|