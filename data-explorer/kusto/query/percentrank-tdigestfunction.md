---
title: percentrank_tdigest () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны percentrank_tdigest() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: fe356ddb2e6301bbb283d2e6aa59b5c98f8bf3fe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511192"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

Высчитывает приблизительный ранг значения в наборе, где ранг выражается в процентах от размера набора. Эту функцию можно рассматривать как обратную процентиль.

**Синтаксис**

`percentrank_tdigest(`*TDigest* `,` *Expr*`)`

**Аргументы**

* *TDigest*: Выражение, которое было сгенерировано [tdigest()](tdigest-aggfunction.md) или [tdigest_merge()](tdigest-merge-aggfunction.md).
* *Expr*: Выражение, представляющее значение, используемое для расчета процентного ранжирования.

**Возвращает**

Процентное значение в наборе данных.

**Советы**

1) Тип второго параметра и тип элементов в tdigest должны быть одинаковыми.

2) Первый параметр должен быть TDigest, который был создан [tdigest()](tdigest-aggfunction.md) или [tdigest_merge()](tdigest-merge-aggfunction.md)

**Примеры**

Получение percentrank_tdigest () ущерба имущества, которое оценивается в 4490 $ составляет 85%:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Column1|
|---|
|85.0015237192293|


Использование процентили 85 над ущербом имущество должно дать 4490 $:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|