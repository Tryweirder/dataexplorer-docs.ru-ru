---
title: make_set_if () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_set_if () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6b26a13539d88aae57774cc35cf57d321b67f4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346904"
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

**См. также:**

[`make_set`](./makeset-aggfunction.md)функция, которая выполняет то же, без выражения предиката.

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