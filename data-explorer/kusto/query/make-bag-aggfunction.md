---
title: make_bag () (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана make_bag (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16f5f5663c4807a766d99c12020ff0a46c4db336
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512994"
---
# <a name="make_bag-aggregation-function"></a>make_bag() (функция агрегирования)

Возвращает `dynamic` (JSON) пакет свойств (словарь) всех значений *Expr* в группе.

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``make_bag(` *Экстр* `,` *-MaxSize*`)`

**Аргументы**

* *Expr*: Выражение `dynamic` типа, который будет использоваться для расчета агрегации.
* *MaxSize* является дополнительным целым пределом для максимального количества возвращенных элементов (по умолчанию *1048576).* Значение MaxSize не может превышать 1048576.

**Примечание**

Устаревший и устаревший вариант `make_dictionary()` этой функции: имеет предел по умолчанию *MaxSize* No 128.

**Возвращает**

Возвращает `dynamic` (JSON) имущество мешок (словарь) всех значений *Expr* в группе, которые собственности мешки (словари).
Значения, не связанные со словарями, будут пропущены.
Если ключ отображается в более чем одной строке, будет выбрано произвольное значение (из возможных значений для этого ключа).

**См. также:**

Используйте [bag_unpack ()](bag-unpackplugin.md) плагин для расширения динамических объектов JSON в столбцы с помощью ключей сумок свойства. 

**Примеры**

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
|"prop01": "val_a", "prop02": "val_b", "prop03": "val_c" |

Используйте [bag_unpack ()](bag-unpackplugin.md) плагин для преобразования ключей сумки в make_bag() выход в столбцы. 

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