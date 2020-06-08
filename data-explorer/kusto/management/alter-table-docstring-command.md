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
ms.openlocfilehash: 65b71ab15763506683c461f04975d22d396f6405
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512543"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Изменяет значение DocString существующей таблицы.

`.alter``table` *TableName* `docstring` *Документация по* TableName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший таблицу, может изменить ее.
> * Если таблица не существует, возвращается ошибка. Сведения о создании новой таблицы см. в статье [Создание таблицы.](create-table-command.md)

**Пример** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
