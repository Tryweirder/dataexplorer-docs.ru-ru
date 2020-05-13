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
ms.openlocfilehash: 7d97d31b2fb97d5541400bc0605ee40e83807b62
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83371898"
---
# <a name="sumif-aggregation-function"></a>СУММЕСЛИ () (агрегатная функция)

Возвращает сумму *выражения expr* , для которой *предикат* принимает значение `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

Можно также использовать функцию [Sum ()](sum-aggfunction.md) , которая суммирует строки без выражения предиката.

**Синтаксис**

суммировать `sumif(` *Expr* `,` *предикат* выражения`)`

**Аргументы**

* *Expr*: выражение для вычисления агрегата. 
* *Предикат*: predicate, при значении true вычисляемое значение *expr*будет добавлено к сумме. 

**Возвращает**

Итоговое значение *выражения expr* , для *Predicate* которого вычисляется предикат `true` .

**Пример**

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