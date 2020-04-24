---
title: row_cumsum () - Исследователь данных Azure (англ.) Документы Майкрософт
description: Эта статья описывает row_cumsum () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92ebec75dcd7e44d59f964dc735e857f22f1ad00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510223"
---
# <a name="row_cumsum"></a>row_cumsum()

Вычисляет совокупную сумму столбца в [наборе серийных строк.](./windowsfunctions.md#serialized-row-set)

**Синтаксис**

`row_cumsum``(` *Срок* `,` и *перезагрузка*`)`

* *Термин* — это выражение, указывающее на значение, подытожилое.
  Выражение должно быть масштабированием одного из следующих `int` `long`типов: `real` `decimal`, , или . Значения null *Term* не влияют на сумму.
* *Перезагрузка* является необязательным `bool` аргументом типа, который указывает, когда операция накопления должна быть перезапущена (установлена обратно на 0). Он может быть использован для обозначения разделов данных; приведены во втором примере ниже.

**Возвращает**

Функция возвращает совокупную сумму своего аргумента.

**Примеры**

Следующий пример показывает, как рассчитать совокупную сумму первых нескольких даже несколько даже несколько.

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

Этот пример показывает, как вычислить `salary`совокупную сумму (здесь, ) `name`при разделении данных (здесь, по ):

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

name   | month  | Зарплата  | total
-------|--------|---------|------
Алиса  | 1      | 1000    | 1000
Алиса  | 2      | 2000    | 3000
Алиса  | 3      | 1400    | 4400
Владимир    | 1      | 1000    | 1000
Владимир    | 2      | 1950    | 2950
Владимир    | 3      | 1450    | 4400