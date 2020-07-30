---
title: СУММЕСЛИ () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается процедура СУММЕСЛИ () (агрегатная функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cd9900b5087ed0d6ae7e97d2f2dad809bb909331
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350814"
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