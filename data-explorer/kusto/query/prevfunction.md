---
title: назад () — Azure обозреватель данных
description: В этой статье описываются предыдущие () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4216f691345c7dffd3bb1974e5f82e877ffb70f2
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128994"
---
# <a name="prev"></a>prev()

Возвращает значение указанного столбца в указанной строке.
Указанная строка находится в указанном смещении от текущей строки в [сериализованном наборе строк](./windowsfunctions.md#serialized-row-set).

**Синтаксис**

Существует несколько возможностей.

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

**Аргументы**

* `column`: Столбец, из которого нужно получить значения.

* `offset`: Смещение для возврата в строках. Если смещение не указано, используется смещение по умолчанию 1.

* `default_value`: Значение по умолчанию, используемое при отсутствии предыдущих строк для получения значения. Если значение по умолчанию не указано, используется значение null.

**Примеры**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
