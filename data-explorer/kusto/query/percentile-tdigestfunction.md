---
title: percentile_tdigest () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны percentile_tdigest () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 655a0693b8e04b1230f6b9e8fe247bd2d77b7ac6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511243"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

Рассчитывает процентили результат tdigest результаты (который был создан [tdigest (или](tdigest-aggfunction.md) [tdigest_merge()](tdigest-merge-aggfunction.md)) )

**Синтаксис**

`percentile_tdigest(`*Expr* `,` *Процентиль1* -`,` *типLiteral*`)`

`percentiles_array_tdigest(`*Expr* `,` *Процентиль1* -`,` *Процентиль2*... `,` *Процентилен*`)`

`percentiles_array_tdigest(`*Expr*`,` *Динамический массив* Expr`)`

**Аргументы**

* *Expr*: Выражение, которое было сгенерировано [tdigest](tdigest-aggfunction.md) или [tdigest_merge()](tdigest-merge-aggfunction.md).
* *Процентиль* является двойной константой, которая определяет процентиль.
* *typeLiteral*: Необязательный тип буквальный `typeof(long)`(например, ). При условии, что набор результатов будет такого типа. 
* *Динамический массив*: список процентилей в динамическом массиве целых или плавающих точек чисел

**Возвращает**

Процентилей / процентилесв значение каждого значения в *Expr*.

**Советы**

1) Функция должна получить по крайней мере один процент (и, возможно,`,` *Percentile2*больше, см. синтаксис выше: *Percentile1* -`,` *Процентилен)* и результатом будет динамический массив, который включает в себя результаты. (такие, [`percentiles()`](percentiles-aggfunction.md)как )
  
2) если только один процент был предоставлен и тип был предоставлен также, то результат будет столбец того же типа, предоставляемый с результатами этого процента (все tdigests должны быть такого типа в этом случае).

3) если *Expr* включает в себя tdigests различных типов, то не обеспечивают тип и результат будет типа динамического. (см. примеры ниже).

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
|[0,0,0]|
|[0,0,62000000]|
|[0,0,110000000]|
|[0,0,1200000]|
|[0,0,250000]|


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
|2007-12-20T11:30:00.00000000"|
|2007-12-31T23:59:00.00000000"|