---
title: row_cumsum () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается row_cumsum () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ad1df20972238bee17217f5d9de19a020b4cbce
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242856"
---
# <a name="row_cumsum"></a>row_cumsum()

Вычисляет совокупную сумму столбца в [сериализованном наборе строк](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Синтаксис

`row_cumsum``(` *Term* [ `,` *перезапуск*]`)`

* *Термин* — это выражение, указывающее значение для суммирования.
  Выражение должно быть скаляром одного из следующих типов: `decimal` , `int` , `long` или `real` . Значения *Терма* NULL не влияют на сумму.
* *Restart* является необязательным аргументом типа `bool` , который указывает, когда должна быть перезапущена операция накопления (установите обратно в 0). Его можно использовать для указания секций данных. см. Второй пример ниже.

## <a name="returns"></a>Результаты

Функция возвращает совокупную сумму ее аргумента.

## <a name="examples"></a>Примеры

В следующем примере показано, как вычислить совокупную сумму первых целых чисел.

```kusto
datatable (a:long) [
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10
]
| where a%2==0
| serialize cs=row_cumsum(a)
```

а    | cs
-----|-----
2    | 2
4    | 6
6    | 12
8    | 20
10   | 30

В этом примере показано, как вычислить совокупную сумму (здесь `salary` ), когда данные секционированы (здесь `name` ):

```kusto
datatable (name:string, month:int, salary:long)
[
    "Alice", 1, 1000,
    "Bob",   1, 1000,
    "Alice", 2, 2000,
    "Bob",   2, 1950,
    "Alice", 3, 1400,
    "Bob",   3, 1450,
]
| order by name asc, month asc
| extend total=row_cumsum(salary, name != prev(name))
```

name   | month  | Указание  | total
-------|--------|---------|------
Алиса  | 1      | 1000    | 1000
Алиса  | 2      | 2000    | 3000
Алиса  | 3      | 1400    | 4400
Владимир    | 1      | 1000    | 1000
Владимир    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400