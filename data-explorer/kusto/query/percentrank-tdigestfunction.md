---
title: percentrank_tdigest () — обозреватель данных Azure
description: В этой статье описывается percentrank_tdigest () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 8220c52b70eec8a0a297c5826fff3a6e2a0483b3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373240"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Вычисляет приблизительный ранг значения в наборе, где ранг выражается в процентах от размера набора. Эта функция может быть просмотрена как обратная часть процентиля.

**Синтаксис**

`percentrank_tdigest(`*Тдижест* `,` *Выражение*`)`

**Аргументы**

* *Тдижест*: выражение, созданное [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Expr*: выражение, представляющее значение, используемое для вычисления процентного рейтинга.

**Возвращает**

Процентный ранг значения в наборе данных.

**"Советы"**

1) Тип второго параметра и тип элементов в тдижест должны совпадать.

2) Первым параметром должен быть Тдижест, созданный [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md)

**Примеры**

Получение percentrank_tdigest () свойства ущерба, которое имеет значение $4490, равно ~ 85%:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Column1|
|---|
|85.0015237192293|


Использование процентиля 85 над свойством ущерба должно дать $4490:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|
