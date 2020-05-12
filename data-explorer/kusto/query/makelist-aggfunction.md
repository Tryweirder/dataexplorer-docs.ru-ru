---
title: make_list () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_list () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: fed2616f5fbd32b1c80f936d5689261467a9dc5e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224840"
---
# <a name="make_list-aggregation-function"></a>make_list () (агрегатная функция)

Возвращает массив `dynamic` (JSON) со всеми значениями *Expr* в группе.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

`summarize``make_list(` *Expr* [ `,` *MAXSIZE*]`)`

**Аргументы**

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.
* *MAXSIZE* — Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

> [!NOTE]
> Устаревший и устаревший вариант этой функции: `makelist()` имеет ограничение по умолчанию *MaxSize* = 128.

**Возвращает**

Возвращает массив `dynamic` (JSON) со всеми значениями *Expr* в группе.
Если входные данные `summarize` оператора не сортируются, порядок элементов в результирующем массиве не определен.
Если входные данные `summarize` оператора сортируются, порядок элементов в результирующем массиве отслеживает входные данные.

> [!TIP]
> Используйте [`mv-apply`](./mv-applyoperator.md) оператор, чтобы создать упорядоченный список с помощью некоторого ключа. Примеры см. [здесь](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).

**См. также:**

[`make_list_if`](./makelistif-aggfunction.md)аналогичен оператору `make_list` , за исключением того, что он также принимает предикат.