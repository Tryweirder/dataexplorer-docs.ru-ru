---
title: make_list_with_nulls () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается make_list_with_nulls (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 4b039008c5969cf02187d69a3486b09e04ec41ae
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512875"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls() (функция агрегирования)

Возвращает `dynamic` массив (JSON) всех значений *Expr* в группе, включая нулевые значения.

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``make_list_with_nulls(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации.

**Возвращает**

Возвращает `dynamic` массив (JSON) всех значений *Expr* в группе, включая нулевые значения.
Если вход оператору `summarize` не отсортирован, порядок элементов в полученном массиве не определен.
Если вход `summarize` оператору отсортирован, порядок элементов в полученном массиве отслеживает ввод.

> [!TIP]
> Используйте [`mv-apply`](./mv-applyoperator.md) оператора для создания заказанный список по некоторым ключам. Примеры см. [здесь](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).
