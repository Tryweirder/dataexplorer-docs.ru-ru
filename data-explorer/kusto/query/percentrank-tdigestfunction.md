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
ms.openlocfilehash: cafb52b7254041a18a9cae956ed338f45bc67a54
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818568"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Вычисляет приблизительный ранг значения в наборе, где ранг выражается в процентах от размера набора.
Эта функция может быть просмотрена как обратная часть процентиля.

**Синтаксис**

`percentrank_tdigest(`*Тдижест* `,` *Выражение*`)`

**Аргументы**

* *Тдижест*: выражение, созданное [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Expr*: выражение, представляющее значение, используемое для вычисления процентного рейтинга.

**Возвращает**

Процентный ранг значения в наборе данных.

**"Советы"**

1) Тип второго параметра и тип элементов `tdigest` должны совпадать.

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
