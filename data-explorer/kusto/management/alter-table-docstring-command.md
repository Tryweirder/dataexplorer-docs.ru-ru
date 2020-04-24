---
title: . ALTER TABLE docstring — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается. ALTER TABLE docstring в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5b3fa21b8b9012fe23a82afea77b1a3e24ae3c91
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108479"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Изменяет значение DocString существующей таблицы.

`.alter``table` *TableName* `docstring` *Документация по* TableName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * Изменение таблицы также разрешено [пользователю базы данных](../management/access-control/role-based-authorization.md) , который изначально создал таблицу.
> * Если таблица не существует, возвращается ошибка. Сведения о создании новой таблицы см [. в статье Создание таблицы.](create-table-command.md)

**Пример** 

```
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```