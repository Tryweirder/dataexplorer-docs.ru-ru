---
title: образец оператора - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается пример оператора в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 757830bde0c56ac727d5240c01ca4768eab28877
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510036"
---
# <a name="sample-operator"></a>Оператор sample

Возвращает до указанного количества случайных строк из таблицы ввода.

```kusto
T | sample 5
```

**Синтаксис**

_T_ `| sample` _NumberOfRows_

**Аргументы**

- _NumberOfRows_: Количество строк _T,_ чтобы вернуться. Можно указать любое численное выражение.

**Примечания**

- `sample`ориентирована на скорость, а не даже распределение ценностей. В частности, это означает, что он не будет производить "справедливые" результаты, `union` если `join` используется после операторов, что союз 2 наборы данных различных размеров (например, или операторов). Рекомендуется использовать `sample` сразу после ссылки на таблицу и фильтры.

- `sample`является недетерминированным оператором и будет возвращать различные наборы результатов каждый раз, когда он оценивается во время запроса. Например, следующий запрос дает два разных строка (даже если можно ожидать возврата одной и той же строки дважды).

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

Для того, чтобы гарантировать, что в примере выше `_sample` рассчитывается один раз, можно использовать [материализовать ()](./materializefunction.md) функцию:

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

**Советы**

- если вы хотите провести образец определенного процента ваших данных (а не определенного количества строк),

```kusto
StormEvents | where rand() < 0.1
```

- Если вы хотите, чтобы образец ключей, а не строки (например - образец [`sample-distinct`](./sampledistinctoperator.md) 10 идентификаторов и получить все строки для этих идентификаторов) вы можете использовать в сочетании с оператором. `in`

**Примеры**

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```