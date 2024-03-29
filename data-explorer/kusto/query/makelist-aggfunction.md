---
title: make_list () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_list () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: bd923724a391cfc1638057fec654a1bd74b78479
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303347"
---
# <a name="make_list-aggregation-function"></a>make_list () (агрегатная функция)

Возвращает массив `dynamic` (JSON) со всеми значениями *Expr* в группе.

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

`summarize``make_list(` *Expr* [ `,` *MAXSIZE*]`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата.
* *MAXSIZE* — Необязательное целочисленное ограничение на максимальное число возвращаемых элементов (по умолчанию — *1048576*). Значение MaxSize не может превышать 1048576.

> [!NOTE]
> `makelist()` — Это устаревшая и устаревшая версия `make_list` функции. Для устаревшей версии используется ограничение по умолчанию *MAXSIZE* = 128.

## <a name="returns"></a>Возвращаемое значение

Возвращает массив `dynamic` (JSON) со всеми значениями *Expr* в группе.
Если входные данные `summarize` оператора не сортируются, порядок элементов в результирующем массиве не определен.
Если входные данные `summarize` оператора сортируются, порядок элементов в результирующем массиве отслеживает входные данные.

> [!TIP]
> Используйте [`array_sort_asc()`](./arraysortascfunction.md) функцию или, [`array_sort_desc()`](./arraysortdescfunction.md) чтобы создать упорядоченный список по определенному ключу.

## <a name="examples"></a>Примеры

### <a name="one-column"></a>Один столбец

Самый простой пример — вывести список из одного столбца:

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name)
```

|милист|
|---|
|["треугольник", "квадратный", "Rectangle", "пятиугольник", "шестиугольник", "хептагон", "Октогон", "Нонагон", "Декагон"]|

### <a name="using-the-by-clause"></a>Использование предложения "by"

В следующем запросе выполняется группирование с помощью `by` предложения:

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name) by isEvenSideCount = sideCount % 2 == 0
```

|милист|исевенсидекаунт|
|---|---|
|false|["треугольник", "пятиугольник", "хептагон", "Нонагон"]|
|true|["квадратный", "Rectangle", "шестиугольник", "Октогон", "Декагон"]|

### <a name="packing-a-dynamic-object"></a>Упаковка динамического объекта

Динамический объект можно [упаковать](./packfunction.md) в столбец перед выводом списка из него, как показано в следующем запросе:

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| extend d = pack("name", name, "sideCount", sideCount)
| summarize mylist = make_list(d) by isEvenSideCount = sideCount % 2 == 0
```

|милист|исевенсидекаунт|
|---|---|
|false|[{"Name": "треугольник", "Сидекаунт": 3}, {"Name": "пятиугольник", "Сидекаунт": 5}, {"Name": "хептагон", "Сидекаунт": 7}, {"Name": "Нонагон", "Сидекаунт": 9}]|
|true|[{"имя": "квадрат", "Сидекаунт": 4}, {"Name": "Rectangle", "Сидекаунт": 4}, {"Name": "шестиугольник", "Сидекаунт": 6}, {"Name": "Октогон", "Сидекаунт": 8}, {"Name": "Декагон", "Сидекаунт": 10}]|

## <a name="see-also"></a>См. также раздел

[`make_list_if`](./makelistif-aggfunction.md) аналогичен оператору `make_list` , за исключением того, что он также принимает предикат.
