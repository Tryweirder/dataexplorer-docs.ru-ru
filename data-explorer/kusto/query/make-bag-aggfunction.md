---
title: make_bag () (агрегатная функция) — обозреватель данных Azure
description: В этой статье описывается агрегатная функция make_bag () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3258a847a526e0e3b6ac8f0186b0a1aaabc3ffe5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103210"
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
