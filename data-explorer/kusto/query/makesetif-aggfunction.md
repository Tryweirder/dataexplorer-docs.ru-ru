---
title: make_set_if () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_set_if () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8fd126728c93cfdfca677c059c338c9fd8f7a155
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246336"
---
# <a name="make_set_if-aggregation-function"></a>make_set_if () (агрегатная функция)

Возвращает `dynamic` массив (JSON) набора уникальных значений, которые *выражение expr* принимает в группе, для которой *предикату* присваивается значение `true` .

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``make_set_if(` *Expr*, *предикат* [ `,` *MAXSIZE*]`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.
* *Предикат*: предикат, который должен иметь значение `true` для *выражения,* добавляемого к результату.
* *MAXSIZE* — Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

## <a name="returns"></a>Результаты

Возвращает `dynamic` массив (JSON) набора уникальных значений, которые *выражение expr* принимает в группе, для которой *предикату* присваивается значение `true` .
Порядок сортировки массива не определен.

> [!TIP]
> Для подсчета только уникальных значений используйте [дкаунтиф ()](dcountif-aggfunction.md) .

## <a name="see-also"></a>См. также раздел

[`make_set`](./makeset-aggfunction.md) функция, которая выполняет то же, без выражения предиката.

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
| summarize make_set_if(name, strlen(name) > 4)
```

|set_name|
|----|
|["Георгия", "Ринго"]|