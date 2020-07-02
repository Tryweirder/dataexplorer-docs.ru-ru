---
title: make_bag_if () (агрегатная функция) — обозреватель данных Azure
description: В этой статье описывается make_bag_if () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2603aab066a7f77ff36553d8898bb713ace990b7
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763809"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if () (агрегатная функция)

Возвращает `dynamic` контейнер (JSON) свойства (словарь) всех значений *выражения "expr"* в группе, для которых *предикату* присвоено значение `true` .

> [!NOTE]
> Может использоваться только в контексте статистической обработки внутри [итоговых](summarizeoperator.md)данных.

**Синтаксис**

`summarize``make_bag_if(` *Expr*, *предикат* [ `,` *MAXSIZE*]`)`

**Аргументы**

* *Expr*: выражение типа `dynamic` , которое будет использоваться для вычисления статистической обработки.
* *Предикат*: предикат, который должен вычислять `true` , чтобы *выражение "expr"* было добавлено к результату.
* *MAXSIZE*: Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

**Возвращает**

Возвращает `dynamic` контейнер свойств (JSON) для всех значений *"expr"* в группе, которые являются контейнерами свойств (словари), для которых *предикат* принимает значение `true` .
Значения, не являющиеся словарными, будут пропущены.
Если ключ отображается в нескольких строках, то будет выбрано произвольное значение из возможных значений для этого ключа.

> [!NOTE]
> [`make_bag`](./make-bag-aggfunction.md)Функция аналогична функции make_bag_if () без выражения предиката.

**Примеры**

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag_if(p, predicate)

```

|dict|
|----|
|{"prop01": "val_a", "prop03": "val_c"} |

Используйте подключаемый модуль [bag_unpack ()](bag-unpackplugin.md) для преобразования ключей контейнера в выходных данных make_bag_if () в столбцы. 

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag_if(p, predicate)
| evaluate bag_unpack(bag)

```

|prop01|prop03|
|---|---|
|val_a|val_c|
