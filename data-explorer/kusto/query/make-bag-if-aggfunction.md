---
title: make_bag_if() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана make_bag_if (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 201de637df387bb8925995a5e52c048255d1535c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512977"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if() (функция агрегирования)

Возвращает `dynamic` (JSON) имущество мешок (словарь) всех значений *Expr* в группе, для `true`которых *Predicate* оценивает .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``make_bag_if(` *Expr*, *Предикат* и`,` *MaxSize*`)`

**Аргументы**

* *Expr*: Выражение `dynamic` типа, который будет использоваться для расчета агрегации.
* *Предикат*: Предикат, который `true`должен оценить, для того, чтобы *Expr* быть добавлены к результату.
* *MaxSize* является дополнительным целым пределом для максимального количества возвращенных элементов (по умолчанию *1048576).* Значение MaxSize не может превышать 1048576.

**Возвращает**

Возвращает `dynamic` (JSON) имущество мешок (словарь) всех значений *Expr* в группе, которые собственности мешки (словари), для которых *Predicate* оценивает `true`.
Значения, не связанные со словарями, будут пропущены.
Если ключ отображается в более чем одной строке, будет выбрано произвольное значение (из возможных значений для этого ключа).

**См. также:**

[`make_bag`](./make-bag-aggfunction.md)функция, которая делает то же самое, без предикатного выражения.

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
|"prop01": "val_a", "prop03": "val_c" |

Используйте [bag_unpack ()](bag-unpackplugin.md) плагин для преобразования ключей сумки в make_bag_if() выход в столбцы. 

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