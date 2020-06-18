---
title: Кэш результатов запроса — Azure обозреватель данных
description: В этой статье описывается кэш результатов запросов в Azure обозреватель данных.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 630266fdaaaac51037a99a6a5243db58a232ae48
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943114"
---
# <a name="query-results-cache"></a>Кэш результатов запроса

Кэш результатов запросов — это кэш, выделенный для хранения результатов запроса. Дополнительные сведения см. в разделе [кэш результатов запроса](../query/query-results-cache.md).

**Команды кэширования результатов запросов**

Kusto предоставляет две команды для управления кэшем и наблюдения за ними:

* [`Show cache`](show-query-results-cache-command.md): Используйте эту команду для отображения статистики, предоставляемой кэшем результатов.

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): Используйте эту команду для очистки кэшированных результатов.
