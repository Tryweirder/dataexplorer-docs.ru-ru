---
title: топ-оператор - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается лучший оператор в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb1d24dd0cd1dfeecb1925e474eb77e8cb86121f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505922"
---
# <a name="top-operator"></a>Оператор top

Возвращает первые записи ( *N* ), отсортированные по указанным столбцам.

```kusto
T | top 5 by Name desc nulls last
```

**Синтаксис**

*T* `| top` *NumberOfRows* `by` `asc` | `desc``nulls first` *Выражение*  | `nulls last`

**Аргументы**

* *NumberOfRows*: Количество строк *T,* чтобы вернуться. Можно указать любое численное выражение.
* *Выражение*: Скалар выражение, с помощью которого сортировать. Значения должны быть представлены числовыми или строковыми значениями, значениями времени или даты.
* `asc` или `desc` (по умолчанию) могут указывать направление выбора элементов в диапазоне: снизу вверх или сверху вниз.
* `nulls first`(по умолчанию `asc` для `nulls last` ордера) `desc` или (по умолчанию для ордера) может показаться, что они контролируют, будут ли нулевые значения в начале или конце диапазона.


**Советы**

* `top 5 by name`эквивалентно выражению `sort by name | take 5` как семантической, так и производительности.
* Используйте [высоковложенного](topnestedoperator.md) оператора для получения иерархических (вложенных) высших результатов.