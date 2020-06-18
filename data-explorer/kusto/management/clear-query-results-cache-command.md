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
ms.openlocfilehash: 9ddb94e005e88855bbeddd7d3cf8ab537a42d413
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943125"
---
# <a name="clear-query-results-cache"></a>Очистить кэш результатов запроса

Очистить все [кэшированные результаты запроса](../query/query-results-cache.md) , выполненные в базе данных контекста.

**Синтаксис**

`.clear` `database` `cache` `queryresults`

**Возвращает**

Эта команда возвращает таблицу со следующими столбцами:

|Столбец    |Type    |Описание
|---|---|---
|NodeId|`string`|Идентификатор узла кластера.
|Записи|`long`|Число записей, сброшенных узлом.

**Пример**

```kusto
.clear database cache queryresults
```

|NodeId|Записи|
|---|---|
|Узел1|42
|Узел2|0
