---
title: . Очистка данных таблицы — Azure обозреватель данных
description: В этой статье описывается `.clear table data` команда в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vrozov
ms.service: data-explorer
ms.topic: reference
ms.date: 10/01/2020
ms.openlocfilehash: 513544b8fb373f7242bd36b250790801b39061b2
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615083"
---
# <a name="clear-table-data"></a>. Очистка данных таблицы

Очищает данные существующей таблицы, включая данные приема потоковой передачи.

`.clear``table` *TableName*`data` 

> [!NOTE]
> * Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md)

**Пример** 

```kusto
.clear table LyricsAsTable data 
```
 
