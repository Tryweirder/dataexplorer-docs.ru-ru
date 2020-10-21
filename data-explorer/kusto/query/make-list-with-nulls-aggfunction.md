---
title: make_list_with_nulls () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_list_with_nulls () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 18d10aa15e25979c0945ec26efb8bfe9a66dfde4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252227"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (агрегатная функция)

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, включая значения NULL.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``make_list_with_nulls(` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.

## <a name="returns"></a>Результаты

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, включая значения NULL.
Если входные данные `summarize` оператора не сортируются, порядок элементов в результирующем массиве не определен.
Если входные данные `summarize` оператора сортируются, порядок элементов в результирующем массиве отслеживает входные данные.

> [!TIP]
> Используйте [`array_sort_asc()`](./arraysortascfunction.md) функцию или, [`array_sort_desc()`](./arraysortdescfunction.md) чтобы создать упорядоченный список по определенному ключу.
