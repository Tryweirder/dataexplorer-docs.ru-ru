---
title: назад () — Azure обозреватель данных
description: В этой статье описываются предыдущие () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ef9fe5160d433554880ac1be0c4a3409d286f17
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249607"
---
# <a name="prev"></a>prev()

Возвращает значение указанного столбца в указанной строке.
Указанная строка находится в указанном смещении от текущей строки в [сериализованном наборе строк](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Синтаксис

Существует несколько возможностей.

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

## <a name="arguments"></a>Аргументы

* `column`: Столбец, из которого нужно получить значения.

* `offset`: Смещение для возврата в строках. Если смещение не указано, используется смещение по умолчанию 1.

* `default_value`: Значение по умолчанию, используемое при отсутствии предыдущих строк для получения значения. Если значение по умолчанию не указано, используется значение null.

## <a name="examples"></a>Примеры

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
