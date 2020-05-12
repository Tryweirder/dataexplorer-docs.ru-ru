---
title: make_list_with_nulls () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_list_with_nulls () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 41f07f16641fd303c9b8e76b4924238378b6ccc9
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224822"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (агрегатная функция)

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, включая значения NULL.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

`summarize``make_list_with_nulls(` *Выражение*`)`

**Аргументы**

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.

**Возвращает**

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, включая значения NULL.
Если входные данные `summarize` оператора не сортируются, порядок элементов в результирующем массиве не определен.
Если входные данные `summarize` оператора сортируются, порядок элементов в результирующем массиве отслеживает входные данные.

> [!TIP]
> Используйте [`mv-apply`](./mv-applyoperator.md) оператор, чтобы создать упорядоченный список с помощью некоторого ключа. Примеры см. [здесь](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).
