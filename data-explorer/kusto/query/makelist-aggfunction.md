---
title: make_list() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана make_list (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e46dbfac7b8c819f66d469c160452ec4dddfb769
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512756"
---
# <a name="make_list-aggregation-function"></a>make_list() (функция агрегирования)

Возвращает массив `dynamic` (JSON) со всеми значениями *Expr* в группе.

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``make_list(` *Экстр* `,` *-MaxSize*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации.
* *MaxSize* является дополнительным целым пределом для максимального количества возвращенных элементов (по умолчанию *1048576).* Значение MaxSize не может превышать 1048576.

> [!NOTE]
> Устаревший и устаревший вариант `makelist()` этой функции: имеет предел по умолчанию *MaxSize* No 128.

**Возвращает**

Возвращает массив `dynamic` (JSON) со всеми значениями *Expr* в группе.
Если вход оператору `summarize` не отсортирован, порядок элементов в полученном массиве не определен.
Если вход `summarize` оператору отсортирован, порядок элементов в полученном массиве отслеживает ввод.

> [!TIP]
> Используйте [`mv-apply`](./mv-applyoperator.md) оператора для создания заказанный список по некоторым ключам. Примеры см. [здесь](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).

**См. также:**

[`make_list_if`](./makelistif-aggfunction.md)оператор похож `make_list`на, за исключением он также принимает предикат.