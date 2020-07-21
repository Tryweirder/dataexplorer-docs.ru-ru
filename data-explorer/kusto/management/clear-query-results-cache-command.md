---
title: Очистка кэша результатов запросов в Azure обозреватель данных
description: В этой статье описывается команда управления для очистки кэшированной схемы базы данных в Azure обозреватель данных.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 68d6493176696f0241467303f166b8c7160859b7
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565437"
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
|Количество|`long`|Число записей, удаленных узлом.

**Пример**

```kusto
.clear database cache queryresults
```

|NodeId|Записи|
|---|---|
|Узел1|42
|Узел2|0
