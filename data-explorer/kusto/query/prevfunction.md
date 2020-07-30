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
ms.openlocfilehash: fb781834d77aee678103a14714721ff0d46f7b3a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346105"
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
