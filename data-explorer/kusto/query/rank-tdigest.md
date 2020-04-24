---
title: rank_tdigest() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны rank_tdigest () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: ea24213b0ca673c39f399c3a12cc54cd7d7f47d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510546"
---
# <a name="rank_tdigest"></a>rank_tdigest()

Вычисляет приблизительный ранг значения в наборе. Ранг значения `v` в `S` наборе определяется как `S` количество членов, `v`которые `S` меньше или равны, представлено его tdigest.

**Синтаксис**

`rank_tdigest(`*TDigest* `,` *Expr*`)`

**Аргументы**

* *TDigest*: Выражение, которое было сгенерировано [tdigest()](tdigest-aggfunction.md) или [tdigest_merge()](tdigest-merge-aggfunction.md)
* *Expr*: Выражение, представляющее значение, используемое для расчета ранжирования.

**Возвращает**

Значение foreach ранга в наборе данных.

**Советы**

1) Значения, которые вы хотите получить его ранг должен быть того же типа, как tdigest.

**Примеры**

В отсортированном списке (1-1000), ранг 685 является его индекс:

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

Этот запрос вычисляет ранг значения 4490$ над всеми затратами свойства повреждения:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

Получение расчетного процента ранга (путем деления на установленный размер):

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


Процентили 85 от ущерба свойства расходы 4490$ :

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


