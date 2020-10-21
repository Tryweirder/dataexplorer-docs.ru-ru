---
title: percentrank_tdigest () — обозреватель данных Azure
description: В этой статье описывается percentrank_tdigest () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: dd784d8968b45a735bd2df840a09c349e2fdcbd2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249679"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Вычисляет приблизительный ранг значения в наборе, где ранг выражается в процентах от размера набора.
Эта функция может быть просмотрена как обратная часть процентиля.

## <a name="syntax"></a>Синтаксис

`percentrank_tdigest(`*Тдижест* `,` *Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Тдижест*: выражение, созданное [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Expr*: выражение, представляющее значение, используемое для вычисления процентного рейтинга.

## <a name="returns"></a>Результаты

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
