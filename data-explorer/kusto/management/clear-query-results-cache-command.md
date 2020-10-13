---
title: Очистка кэша результатов запросов в Azure обозреватель данных
description: Узнайте, как очистить кэшированные результаты запроса в Azure обозреватель данных. Узнайте, какую команду использовать, и см. пример.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 72678453211ada2a6366b771153eeb11a717d7a3
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002954"
---
# <a name="clear-query-results-cache"></a>Очистить кэш результатов запроса

Очистить все [кэшированные результаты запроса](../query/query-results-cache.md) , выполненные в базе данных контекста.

**Синтаксис**

`.clear` `database` `cache` `query_results`

**Возвращает**

Эта команда возвращает таблицу со следующими столбцами:

|Столбец    |Type    |Описание
|---|---|---
|NodeId|`string`|Идентификатор узла кластера.
|Count|`long`|Число записей, удаленных узлом.

**Пример**

```kusto
.clear database cache queryresults
```

|NodeId|Записи|
|---|---|
|Узел1|42
|Узел2|0
