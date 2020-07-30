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
ms.openlocfilehash: 67e408653a4873dce3b5e8f21a91775573affbe2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347023"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if () (агрегатная функция)

Возвращает `dynamic` контейнер (JSON) свойства (словарь) всех значений *выражения "expr"* в группе, для которых *предикату* присвоено значение `true` .

> [!NOTE]
> Может использоваться только в контексте статистической обработки внутри [итоговых](summarizeoperator.md)данных.

## <a name="syntax"></a>Синтаксис

`summarize``make_bag_if(` *Expr*, *предикат* [ `,` *MAXSIZE*]`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение типа `dynamic` , которое будет использоваться для вычисления статистической обработки.
* *Предикат*: предикат, который должен вычислять `true` , чтобы *выражение "expr"* было добавлено к результату.
* *MAXSIZE*: Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

## <a name="returns"></a>Результаты

Возвращает `dynamic` контейнер свойств (JSON) для всех значений *"expr"* в группе, которые являются контейнерами свойств (словари), для которых *предикат* принимает значение `true` .
Значения, не являющиеся словарными, будут пропущены.
Если ключ отображается в нескольких строках, то будет выбрано произвольное значение из возможных значений для этого ключа.

> [!NOTE]
> [`make_bag`](./make-bag-aggfunction.md)Функция аналогична функции make_bag_if () без выражения предиката.

## <a name="examples"></a>Примеры

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
