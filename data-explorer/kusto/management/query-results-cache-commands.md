---
title: Кэш результатов запроса — Azure обозреватель данных
description: В этой статье описывается кэш результатов запросов в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: fa2bf2f6d24162c5bdb1c851ef7d74e4eb39489f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349913"
---
# <a name="query-results-cache"></a>Кэш результатов запроса

Кэш результатов запросов — это кэш, выделенный для хранения результатов запроса. Дополнительные сведения см. в разделе [кэш результатов запроса](../query/query-results-cache.md).

**Команды кэширования результатов запросов**

Kusto предоставляет две команды для управления кэшем и наблюдения за ними:

* [`Show cache`](show-query-results-cache-command.md): Используйте эту команду для отображения статистики, предоставляемой кэшем результатов.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Используйте эту команду для очистки кэшированных результатов.
