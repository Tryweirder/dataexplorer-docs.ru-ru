---
title: percentile_tdigest () — обозреватель данных Azure
description: В этой статье описывается percentile_tdigest () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 7111f34fcd42e025b22960aa013310d7e4b672fd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252165"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

Вычисляет результат процентиля на основе `tdigest` результатов (созданных [тдижест ()](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md))

## <a name="syntax"></a>Синтаксис

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *типелитерал*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*]... [ `,` *Перцентилен*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Динамический массив*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, созданное [`tdigest`](tdigest-aggfunction.md) или [tdigest_merge ()](tdigest-merge-aggfunction.md).
* *Процентиль* — это двойная константа, указывающая процентиль.
* *типелитерал*: необязательный литерал типа (например, `typeof(long)` ). Если он указан, результирующий набор будет иметь такой тип. 
* *Динамический массив*. список процентили в динамическом массиве целочисленных значений с плавающей запятой.

## <a name="returns"></a>Результаты

Значение процентили/перцентилесв для каждого значения в *`Expr`* .

**Советы**

* Функция должна получить по крайней мере один процент (и, возможно, более, см. синтаксис, приведенный выше: *Percentile1* [ `,` *Percentile2*]... [ `,` *Перцентилен*]), а результат будет динамическим массивом, содержащим результаты. (например, [`percentiles()`](percentiles-aggfunction.md) )
  
* Если предоставлен только один процент, а также предоставлен тип, то результатом будет столбец того же типа, что и результаты этого процента. В этом случае все `tdigest` функции должны иметь такой тип.

* Если *`Expr`* содержит `tdigest` функции различных типов, не следует указывать тип. Результат будет иметь тип Dynamic. Ознакомьтесь с приведенными ниже примерами.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
