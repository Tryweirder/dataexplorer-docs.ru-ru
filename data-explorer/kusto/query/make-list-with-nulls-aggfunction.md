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
ms.openlocfilehash: b78bed51da8422dced4d57406c10721639b68ccc
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346989"
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
> Используйте [`mv-apply`](./mv-applyoperator.md) оператор, чтобы создать упорядоченный список с помощью некоторого ключа. Примеры см. [здесь](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).
