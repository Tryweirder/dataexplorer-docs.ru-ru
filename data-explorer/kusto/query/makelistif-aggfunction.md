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
ms.openlocfilehash: 9090e752f018c4abcce759c37a8ecb3571e2fbd6
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102910"
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

## <a name="see-also"></a>См. также раздел

[`make_list`](./makelist-aggfunction.md) функция, которая выполняет то же, без выражения предиката.