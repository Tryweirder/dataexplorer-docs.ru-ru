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
ms.openlocfilehash: 89e9eea4e8a6a5922e9141818fc5832156ac8e72
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803562"
---
# <a name="sample-operator"></a>Оператор sample

Возвращает указанное число случайных строк из входной таблицы.

```kusto
T | sample 5
```

> [!NOTE]
> * `sample`ориентирован на скорость, а не на равномерное распределение значений. В частности, это означает, что он не будет выдавать "честные" результаты, если они используются после операторов, объединяющих два набора данных разного размера (например, `union` `join` операторы или). Рекомендуется использовать `sample` сразу после ссылки на таблицу и фильтров.
> * `sample`является недетерминированным оператором и возвращает другой результирующий набор каждый раз, когда он вычисляется во время выполнения запроса. Например, следующий запрос выдает две различные строки (даже если бы одна строка возвращалась дважды).

## <a name="syntax"></a>Синтаксис

*T* `| sample` *NumberOfRows*

## <a name="arguments"></a>Аргументы

* *NumberOfRows*: число возвращаемых строк *T* . Можно указать любое числовое выражение.

## <a name="examples"></a>Примеры

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

Для выборки определенного процента данных (а не указанного числа строк) можно использовать

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

Для выборки ключей, а не строк (например, 10 идентификаторов и получения всех строк для этих идентификаторов) можно использовать [`sample-distinct`](./sampledistinctoperator.md) в сочетании с `in` оператором.


<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
