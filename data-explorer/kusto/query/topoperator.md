---
title: Оператор Top — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор TOP в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aadb0b41e666b5b0e90fa33b7ddc30e363e985c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241237"
---
# <a name="top-operator"></a>Оператор top

Возвращает первые *N* записей, отсортированных по указанным столбцам.

```kusto
T | top 5 by Name desc nulls last
```

## <a name="syntax"></a>Синтаксис

*T* `| top` *NumberOfRows* `by` *выражение* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ]

## <a name="arguments"></a>Аргументы

* *NumberOfRows*: число возвращаемых строк *T* . Можно указать любое числовое выражение.
* *Выражение*: скалярное выражение, по которому выполняется сортировка. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
* `asc` или `desc` (по умолчанию) могут указывать направление выбора элементов в диапазоне: снизу вверх или сверху вниз.
* `nulls first` (по умолчанию для `asc` Order) или `nulls last` (по умолчанию для `desc` Order) может использоваться для управления тем, будут ли значения NULL находиться в начале или конце диапазона.

> [!TIP]
> `top 5 by name` эквивалентно выражению `sort by name | take 5` как от семантических, так и с точки зрения производительности.

## <a name="see-also"></a>См. также раздел 

* Используйте оператор [Top-Nested](topnestedoperator.md) для создания иерархических (вложенных) лучших результатов.
