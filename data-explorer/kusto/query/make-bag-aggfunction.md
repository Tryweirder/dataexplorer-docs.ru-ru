---
title: make_bag () (агрегатная функция) — обозреватель данных Azure
description: В этой статье описывается агрегатная функция make_bag () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41eb9703f1718bb43b41927f9ec4d7cac52d67c4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249949"
---
# <a name="make_bag-aggregation-function"></a>make_bag () (агрегатная функция)

Возвращает `dynamic` (JSON) контейнер свойств (Dictionary) всех значений *`Expr`* в группе.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``make_bag(` *`Expr`* [ `,` *MAXSIZE*]`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение типа `dynamic` , используемое для статистических вычислений.
* *MAXSIZE* — Необязательное целочисленное ограничение на максимальное число возвращаемых элементов. Значение по умолчанию — *1048576*. Значение MaxSize не может превышать *1048576*.

**Примечание**.

Устаревший и устаревший вариант функции `make_dictionary()` имеет ограничение по умолчанию *MaxSize* = 128.

## <a name="returns"></a>Результаты

Возвращает `dynamic` контейнер свойств (JSON) всех значений *`Expr`* в группе, которые являются контейнерами свойств.
Значения, не являющиеся словарными, будут пропущены.
Если ключ отображается в нескольких строках, то будет выбрано произвольное значение из возможных значений для этого ключа.

## <a name="see-also"></a>См. также раздел

Используйте подключаемый модуль [bag_unpack ()](bag-unpackplugin.md) для расширения динамических объектов JSON в столбцы, использующие ключи контейнера свойств. 

## <a name="examples"></a>Примеры

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag(p)

```

|dict|
|----|
|{"prop01": "val_a", "prop02": "val_b", "prop03": "val_c"} |

Используйте подключаемый модуль [bag_unpack ()](bag-unpackplugin.md) для преобразования ключей контейнера в выходных данных make_bag () в столбцы. 

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag(p)
| evaluate bag_unpack(bag) 

```

|prop01|prop02|prop03|
|---|---|---|
|val_a|val_b|val_c|
