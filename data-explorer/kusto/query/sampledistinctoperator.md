---
title: Оператор Sample-DISTINCT в Azure обозреватель данных
description: В этой статье описывается оператор Sample-DISTINCT в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3cb1de08604964d4d71c5868ef7564c728b1f2c4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351545"
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
