---
title: пример оператора — Azure обозреватель данных
description: В этой статье описывается пример оператора в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: b5d0624504744bb28dfdb68ee27c48b2119242b8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351511"
---
# <a name="sample-operator"></a>Оператор sample

Возвращает указанное число случайных строк из входной таблицы.

```kusto
T | sample 5
```

## <a name="syntax"></a>Синтаксис

_T_ `| sample` _NumberOfRows_

## <a name="arguments"></a>Аргументы

- _NumberOfRows_: число возвращаемых строк _T_ . Можно указать любое числовое выражение.

**Примечания**

- `sample`ориентирован на скорость, а не на равномерное распределение значений. В частности, это означает, что он не будет выдавать "честные" результаты, если они используются после операторов, объединяющих два набора данных разного размера (например, `union` `join` операторы или). Рекомендуется использовать `sample` сразу после ссылки на таблицу и фильтров.

- `sample`является недетерминированным оператором и возвращает другой результирующий набор каждый раз, когда он вычисляется во время выполнения запроса. Например, следующий запрос выдает две различные строки (даже если бы одна строка возвращалась дважды).

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

Чтобы убедиться, что в приведенном выше примере `_sample` вычисляется один раз, можно использовать функцию [материализации ()](./materializefunction.md) :

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

- Если требуется вычислить определенный процент данных (а не заданное число строк), можно использовать

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

- Если необходимо выполнить выборку ключей, а не строк (например, 10 идентификаторов и получить все строки для этих идентификаторов), можно использовать [`sample-distinct`](./sampledistinctoperator.md) в сочетании с `in` оператором.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
