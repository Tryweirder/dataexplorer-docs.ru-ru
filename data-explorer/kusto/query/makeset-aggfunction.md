---
title: make_set () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_set () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e6eb481423e31e4dfa1b4e6c738ffb525e9aaef7
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618413"
---
# <a name="make_set-aggregation-function"></a>make_set () (агрегатная функция)

Возвращает массив `dynamic` (JSON) с набором различных значений, которые выражение *Expr* принимает в группе.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

`summarize``make_set(` *Expr* [`,` *MAXSIZE*]`)`

**Аргументы**

* *Expr*: выражение для вычисления агрегата.
* *MAXSIZE* — Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

> [!NOTE]
> Устаревший и устаревший вариант этой функции: `makeset()` имеет ограничение по умолчанию *MAXSIZE* = 128.

**Возвращает**

Возвращает массив `dynamic` (JSON) с набором различных значений, которые выражение *Expr* принимает в группе.
Порядок сортировки массива не определен.

> [!TIP]
> Для подсчета только уникальных значений используйте [DCount ()](dcount-aggfunction.md) .

**Пример**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Создать":::

**См. также:**

* Используйте [`mv-expand`](./mvexpandoperator.md) оператор для противоположной функции.
* [`make_set_if`](./makesetif-aggfunction.md)аналогичен оператору `make_set`, за исключением того, что он также принимает предикат.