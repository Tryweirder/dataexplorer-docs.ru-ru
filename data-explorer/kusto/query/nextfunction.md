---
title: далее () - Исследователь данных Azure (англ.) Документы Майкрософт
description: Эта статья описывает следующую () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f45e88942fdf9eb23451e1391866f57ca5f0e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512127"
---
# <a name="next"></a>next()

Возвращает значение столбца в строку, что он в некоторых смещения после текущего строки в [наборе серийного ряда.](./windowsfunctions.md#serialized-row-set)

**Синтаксис**

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

**Аргументы**

* `column`: столбец, чтобы получить значения от.

* `offset`: смещение, чтобы идти вперед в строках. При отсутствии смещения указывается, используется смещение по умолчанию 1.

* `default_value`: значение по умолчанию, используемое при отсутствии следующих строк, из которые можно было бы взять значение. Когда значение по умолчанию не указано, используется null.


**Примеры**
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```