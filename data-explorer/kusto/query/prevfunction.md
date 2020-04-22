---
title: prev() - Azure Data Explorer Документы Майкрософт
description: В этой статье описанprev() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33f6045333826b21ddc0092e2cc7d5e033c12a96
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744595"
---
# <a name="prev"></a>prev()

Возвращает значение столбца в строку, которое он в некоторой смещении до текущего ряда в [наборе серийного ряда.](./windowsfunctions.md#serialized-row-set)

**Синтаксис**

`prev(column)`

`prev(column, offset)`

`prev(column, offset, default_value)`

**Аргументы**

* `column`: столбец, чтобы получить значения от.

* `offset`: смещение, чтобы вернуться в ряды. При отсутствии смещения указывается, используется смещение по умолчанию 1.

* `default_value`: значение по умолчанию, используемое при отсутствии предыдущих строк, из которые можно было бы взять значение. Когда значение по умолчанию не указано, используется null.


**Примеры**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```