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
ms.openlocfilehash: c53faca94e273bf816abcfa34ed400708a7433a3
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169563"
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
> Используйте [`array_sort_asc()`](./arraysortascfunction.md) функцию или, [`array_sort_desc()`](./arraysortdescfunction.md) чтобы создать упорядоченный список по определенному ключу.
