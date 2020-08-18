---
title: Команды кэша результатов запросов — Azure обозреватель данных
description: В этой статье описывается кэш результатов запросов в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 519560f0b6bb98651dc4a8b6749935ec9843eb9c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512584"
---
# <a name="query-results-cache-commands"></a>Команды кэширования результатов запросов

Кэш результатов запросов — это кэш, выделенный для хранения результатов запроса. Дополнительные сведения см. в разделе [кэш результатов запроса](../query/query-results-cache.md).

**Команды кэширования результатов запросов**

Kusto предоставляет две команды для управления кэшем и наблюдения за ними:

* [`Show cache`](show-query-results-cache-command.md): Используйте эту команду для отображения статистики, предоставляемой кэшем результатов.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Используйте эту команду для очистки кэшированных результатов.
