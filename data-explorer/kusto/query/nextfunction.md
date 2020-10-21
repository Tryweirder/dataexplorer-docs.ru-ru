---
title: Next () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается следующее () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ca9361c0a43a2881f7448312e4f8a5129426e55a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248728"
---
# <a name="next"></a>next()

Возвращает значение столбца в строке, который находится в некоторой позиции после текущей строки в [сериализованном наборе строк](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Синтаксис

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

## <a name="arguments"></a>Аргументы

* `column`: столбец, из которого нужно получить значения.

* `offset`: смещение для перехода в строки вперед. Если смещение не указано, используется смещение по умолчанию 1.

* `default_value`: значение по умолчанию, используемое при отсутствии следующих строк для получения значения. Если значение по умолчанию не указано, используется значение null.


## <a name="examples"></a>Примеры
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```