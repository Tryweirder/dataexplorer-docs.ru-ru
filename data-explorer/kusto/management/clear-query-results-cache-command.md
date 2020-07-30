---
title: Очистка кэша результатов запросов в Azure обозреватель данных
description: В этой статье описывается команда управления для очистки кэшированной схемы базы данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 27806155d105a109c7419d04d945fbc854c44286
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349930"
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
|Счетчик|`long`|Число записей, удаленных узлом.

**Пример**

```kusto
.clear database cache queryresults
```

|NodeId|Записи|
|---|---|
|Узел1|42
|Узел2|0
