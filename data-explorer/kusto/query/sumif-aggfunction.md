---
title: sumif() (функция агрегации) - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны sumif() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7d2c96f73b404e8d9acbe9da9defecd6bf1bbf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506670"
---
# <a name="sumif-aggregation-function"></a>sumif() (функция агрегирования)

Возвращает сумму *Expr,* для которых *Predicate* оценивает . `true`

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

Вы также можете использовать функцию [сумма ()](sum-aggfunction.md) которая суммирует строки без выражения предиката.

**Синтаксис**

резюмировать `sumif(` *Expr*`,`*Predicate*`)`

**Аргументы**

* *Expr*: выражение для расчета агрегации. 
* *Предикат*: Предикат, что, если это правда, *Вычисляемой*стоимости будет добавлен к сумме. 

**Возвращает**

Сумма значение *Expr,* для которого *Predicate* оценивает . `true`

> [!TIP]
> Используйте `summarize sumif(expr, filter)` вместо `where filter | summarize sum(expr)`

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