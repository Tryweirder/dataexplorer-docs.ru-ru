---
title: . ALTER TABLE docstring — Azure обозреватель данных
description: В этой статье описывается `.alter table docstring` команда в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: fc449cb6a376eba66457855173c6e992e6ca1dbc
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321664"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Изменяет значение DocString существующей таблицы.

`.alter``table` *TableName* `docstring` *Документация по* TableName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший таблицу, может изменить ее.
> * Если таблица не существует, возвращается ошибка. Сведения о создании новой таблицы см. в разделе [`.create table`](create-table-command.md)

**Пример** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
