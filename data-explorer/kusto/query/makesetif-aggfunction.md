---
title: make_set_if() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана make_set_if (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1393e063fb0abb91b38a8b9e1edc0110e78b3638
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512654"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if() (функция агрегирования)

Возвращает `dynamic` массив (JSON) набора различных значений, которые *Expr* принимает в группе, `true`для которой *Predicate* оценивает .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``make_set_if(` *Expr*, *Предикат* и`,` *MaxSize*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации.
* *Предикат*: Предикат, который `true` должен оценить для *Expr,* чтобы быть добавлены к результату.
* *MaxSize* является дополнительным целым пределом для максимального количества возвращенных элементов (по умолчанию *1048576).* Значение MaxSize не может превышать 1048576.

**Возвращает**

Возвращает `dynamic` массив (JSON) набора различных значений, которые *Expr* принимает в группе, `true`для которой *Predicate* оценивает .
Порядок сортировки массива не определен.

> [!TIP]
> Чтобы только подсчитать различные значения, используйте [dcountif()](dcountif-aggfunction.md)

**См. также:**

[`make_set`](./makeset-aggfunction.md)функция, которая делает то же самое, без предикатного выражения.

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
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|"Джордж", "Ринго""|