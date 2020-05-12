---
title: percentile_tdigest () — обозреватель данных Azure
description: В этой статье описывается percentile_tdigest () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: f2e5e4aca145e0d78baddd7b1e34ab3ce6d047d1
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225009"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

Вычисляет результат процентиля на основе `tdigest` результатов (созданных [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md))

**Синтаксис**

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *типелитерал*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*]... [ `,` *Перцентилен*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Динамический массив*`)`

**Аргументы**

* *Expr*: выражение, созданное [`tdigest`](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Процентиль* — это двойная константа, указывающая процентиль.
* *типелитерал*: необязательный литерал типа (например, `typeof(long)` ). Если он указан, результирующий набор будет иметь такой тип. 
* *Динамический массив*. список процентили в динамическом массиве целочисленных значений с плавающей запятой.

**Возвращает**

Значение процентили/перцентилесв для каждого значения в *`Expr`* .

**"Советы"**

* Функция должна получить по крайней мере один процент (и, возможно, более, см. синтаксис, приведенный выше: *Percentile1* [ `,` *Percentile2*]... [ `,` *Перцентилен*]), а результат будет динамическим массивом, содержащим результаты. (например, [`percentiles()`](percentiles-aggfunction.md) )
  
* Если предоставлен только один процент, а также предоставлен тип, то результатом будет столбец того же типа, что и результаты этого процента. В этом случае все `tdigest` функции должны иметь такой тип.

* Если *`Expr`* содержит `tdigest` функции различных типов, не следует указывать тип. Результат будет иметь тип Dynamic. Ознакомьтесь с приведенными ниже примерами.

**Примеры**

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62000000|
|110000000|
|1200000|
|250 000|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0, 0, 0]|
|[0, 0, 62000000]|
|[0, 0, 110000000]|
|[0, 0, 1200000]|
|[0, 0, 250000]|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62000000]|
|["2007-12-20T11:30:00.0000000 Z"]|
|["2007-12-31T23:59:00.0000000 Z"]|
