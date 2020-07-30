---
title: Next () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается следующее () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a265d536f655df3086ece1b9953eaade4717781c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346632"
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