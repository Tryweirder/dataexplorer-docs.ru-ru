---
title: rank_tdigest () — обозреватель данных Azure
description: В этой статье описывается rank_tdigest () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: a849cd496d41ad473768b3f267639eaf8c467880
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741768"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Вычисляет приблизительный ранг значения в наборе. `v` Ранг значения `S` в наборе определяется как число элементов `S` , которые меньше или равны `v`, `S` представляется его `tdigest`свойством.

**Синтаксис**

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

**Аргументы**

* *Тдижест*: выражение, созданное [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md)
* *Expr*: выражение, представляющее значение, используемое для вычисления ранжирования.

**Возвращает**

Значение "ранг foreach" в наборе данных.

**"Советы"**

1) Значения, для которых необходимо получить ранг, должны иметь тот же тип, что и `tdigest`.

**Примеры**

В отсортированном списке (1-1000) рангом 685 является его индекс:

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Этот запрос вычисляет ранг значения $4490 по всем свойствам, затратам на повреждение:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Получение оценочного процента от ранга (путем деления на размер набора):

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


85-й процентиль для свойств ущерба — $4490:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


