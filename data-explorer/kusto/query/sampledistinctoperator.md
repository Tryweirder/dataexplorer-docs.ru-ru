---
title: Оператор Sample-DISTINCT в Azure обозреватель данных
description: В этой статье описывается оператор Sample-DISTINCT в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd8385a5dc8f959e1fe195bfe333a6868f55cb4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242731"
---
# <a name="sample-distinct-operator"></a>Оператор sample-distinct

Возвращает один столбец, который содержит указанное число разных значений запрошенного столбца. 

по умолчанию только разновидность оператора пытается вернуть ответ как можно быстрее (а не пытаться сделать пример).

```kusto
T | sample-distinct 5 of DeviceId
```

## <a name="syntax"></a>Синтаксис

*T* `| sample-distinct` *нумберофвалуес* `of` *ColumnName*

## <a name="arguments"></a>Аргументы
* *Нумберофвалуес*: количество различных значений *T* для возврата. Можно указать любое числовое выражение.

**Советы**

 Можно использовать для выборки заполнения, поместив `sample-distinct` инструкцию Let и последующий фильтр с помощью `in` оператора (см. пример). 

 Если вам нужны верхние значения, а не просто образец, можно использовать оператор [Top-hitters](tophittersoperator.md) . 

 Если требуется вычислить образцы строк данных (а не значения определенного столбца), см. [оператор Sample](sampleoperator.md) .

## <a name="examples"></a>Примеры  

Получение 10 различных значений из Генеральной совокупности

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

Выполняет выборку заполнения и дальнейшие вычисления, учитывая то, что одна строка не может превысить ограничения запроса. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```
