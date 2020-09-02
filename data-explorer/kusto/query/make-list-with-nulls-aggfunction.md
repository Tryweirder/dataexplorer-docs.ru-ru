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
ms.openlocfilehash: 1d4dafdab4c727b89838f18e13b016d771262f08
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366016"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (агрегатная функция)

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, включая значения NULL.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``make_list_with_nulls(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.

## <a name="returns"></a>Возвращаемое значение

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, включая значения NULL.
Если входные данные `summarize` оператора не сортируются, порядок элементов в результирующем массиве не определен.
Если входные данные `summarize` оператора сортируются, порядок элементов в результирующем массиве отслеживает входные данные.

> [!TIP]
> Используйте [`mv-apply`](./mv-applyoperator.md) оператор, чтобы создать упорядоченный список с помощью некоторого ключа. Примеры см. [здесь](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-make_list-aggregate-by-some-key).
