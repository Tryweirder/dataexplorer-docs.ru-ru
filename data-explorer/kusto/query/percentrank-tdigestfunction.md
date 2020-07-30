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
ms.openlocfilehash: 33eb35e51f403a3c0b7a2f030604b12c705221ea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346173"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Вычисляет приблизительный ранг значения в наборе, где ранг выражается в процентах от размера набора.
Эта функция может быть просмотрена как обратная часть процентиля.

## <a name="syntax"></a>Синтаксис

`percentrank_tdigest(`*Тдижест* `,` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Тдижест*: выражение, созданное [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Expr*: выражение, представляющее значение, используемое для вычисления процентного рейтинга.

## <a name="returns"></a>Возвращаемое значение

Процентный ранг значения в наборе данных.

**Советы**

1) Тип второго параметра и тип элементов `tdigest` должны совпадать.

2) Первым параметром должен быть Тдижест, созданный [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md)

## <a name="examples"></a>Примеры

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
