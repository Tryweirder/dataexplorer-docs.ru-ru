---
title: make_list_if () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается make_list_if (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b34c1dad7be709145c622c97b357734c25292bba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512739"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if() (функция агрегирования)

Возвращает `dynamic` массив (JSON) всех значений *Expr* в группе, для которого `true` *Predicate* оценивает .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``make_list_if(` *Expr*, *Предикат* и`,` *MaxSize*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации.
* *Предикат*: Предикат, который `true`должен оценить, для того, чтобы *Expr* быть добавлены к результату.
* *MaxSize* является дополнительным целым пределом для максимального количества возвращенных элементов (по умолчанию *1048576).* Значение MaxSize не может превышать 1048576.

**Возвращает**

Возвращает `dynamic` массив (JSON) всех значений *Expr* в группе, для которого `true` *Predicate* оценивает .
Если вход оператору `summarize` не отсортирован, порядок элементов в полученном массиве не определен.
Если вход `summarize` оператору отсортирован, порядок элементов в полученном массиве отслеживает ввод.

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
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|"Джордж", "Ринго""|

**См. также:**

[`make_list`](./makelist-aggfunction.md)функция, которая делает то же самое, без предикатного выражения.