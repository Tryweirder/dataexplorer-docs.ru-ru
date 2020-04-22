---
title: оператор, отличаясь выборкой - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор, отличающийся от примеров в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6d6c77aef3a7e2c6d99af792062d9f1a6215f51
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663635"
---
# <a name="sample-distinct-operator"></a>Оператор sample-distinct

Возвращает один столбец, который содержит указанное число разных значений запрошенного столбца. 

по умолчанию (и в настоящее время только) вкус оператора пытается вернуть ответ как можно быстрее (а не пытается сделать справедливый образец)

```kusto
T | sample-distinct 5 of DeviceId
```

**Синтаксис**

*T* `| sample-distinct` *NumberOfValues* `of` *ColumnName*

**Аргументы**
* *NumberOfValues*: Число различных значений *T,* чтобы вернуться. Можно указать любое численное выражение.

**Советы**

 Может быть удобно, чтобы `sample-distinct` попробовать население, поставив в `in` пусть заявление, а затем фильтр с помощью оператора (см. пример) 

 Если вы хотите верхние значения, а не просто образец, вы можете использовать [топ-нападающих](tophittersoperator.md) оператора 

 если вы хотите выставить строки данных (а не значения конкретного столбца), обратитесь к [оператору выборки](sampleoperator.md)

**Примеры**  

Получите 10 различных значений от населения

```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

Выполняет выборку заполнения и дальнейшие вычисления, учитывая то, что одна строка не может превысить ограничения запроса. 

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```