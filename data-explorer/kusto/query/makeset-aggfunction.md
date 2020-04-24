---
title: make_set () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана make_set (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: db11bd528703323d54ff96b228c0b80bbcb86dd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512688"
---
# <a name="make_set-aggregation-function"></a>make_set() (функция агрегирования)

Возвращает массив `dynamic` (JSON) с набором различных значений, которые выражение *Expr* принимает в группе.

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``make_set(` *Экстр* `,` *-MaxSize*`)`

**Аргументы**

* *Expr*: Выражение для расчета агрегации.
* *MaxSize* является дополнительным целым пределом для максимального количества возвращенных элементов (по умолчанию *1048576).* Значение MaxSize не может превышать 1048576.

> [!NOTE]
> Устаревший и устаревший вариант `makeset()` этой функции: имеет предел по умолчанию *MaxSize* No 128.

**Возвращает**

Возвращает массив `dynamic` (JSON) с набором различных значений, которые выражение *Expr* принимает в группе.
Порядок сортировки массива не определен.

> [!TIP]
> Чтобы считать только определенные значения, используйте [dcount()](dcount-aggfunction.md)

**Пример**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

![альт текст](./images/aggregations/makeset.png "makeset")

**См. также:**

* Используйте [`mv-expand`](./mvexpandoperator.md) оператора для противоположной функции.
* [`make_set_if`](./makesetif-aggfunction.md)оператор похож `make_set`на, за исключением он также принимает предикат.