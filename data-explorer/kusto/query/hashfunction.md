---
title: хэш() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается хэш() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f8142c42dcb0874dfbd84515e56dc8765bcba3d7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514150"
---
# <a name="hash"></a>hash()

Возвращает значение хэша для вхотоза.

**Синтаксис**

`hash(`*источник* `,` и *мод*`)`

**Аргументы**

* *источник*: Значение, необходимое для хэшена.
* *mod*: Дополнительное значение модуля, которое должно быть применено к результату хэша, так что выходное значение находится между `0` и *мод* - 1

**Возвращает**

Значение хэша данного скализа, modulo данного значения мода (если указано).

> [!WARNING]
> Алгоритм, используемый для расчета хэша, является xxhash.
> Этот алгоритм может измениться в будущем, и единственная гарантия заключается в том, что в рамках одного запроса все вызовы этого метода используют один и тот же алгоритм.
> Следовательно, пользователям рекомендуется не хранить `hash()` результаты в таблице. Если требуются сохраняющиеся значения хэша, следует использовать вместо этого [hash_sha256()](./sha256hashfunction.md) `hash()`(но обратите внимание, что вычислить его гораздо сложнее, чем).

**Примеры**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

В следующем примере используется функция хэша для выполнения запроса на 10% данных, полезно использовать функцию хэша для выборки данных при условии равномерного распределения значения (в этом примере значение StartTime)

```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```