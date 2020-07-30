---
title: make_list_if () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_list_if () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dda177c39959f860ad7e019371133f16e1de91e2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346938"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if () (агрегатная функция)

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, для которой *предикату* присвоено значение `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``make_list_if(` *Expr*, *предикат* [ `,` *MAXSIZE*]`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.
* *Предикат*: предикат, который должен вычислять `true` , чтобы *выражение* было добавлено к результату.
* *MAXSIZE* — Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

## <a name="returns"></a>Результаты

Возвращает `dynamic` массив (JSON) всех значений *expr* в группе, для которой *предикату* присвоено значение `true` .
Если входные данные `summarize` оператора не сортируются, порядок элементов в результирующем массиве не определен.
Если входные данные `summarize` оператора сортируются, порядок элементов в результирующем массиве отслеживает входные данные.

## <a name="example"></a>Пример

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|["Георгия", "Ринго"]|

**См. также:**

[`make_list`](./makelist-aggfunction.md)функция, которая выполняет то же, без выражения предиката.