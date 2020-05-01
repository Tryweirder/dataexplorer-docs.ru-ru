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
ms.openlocfilehash: 790cd9805be6dd4440ef2eb51c504044dc069b3c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617788"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Изменяет значение DocString существующей таблицы.

`.alter``table` *TableName* `docstring` *Документация по* TableName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * Изменение таблицы также разрешено [пользователю базы данных](../management/access-control/role-based-authorization.md) , который изначально создал таблицу.
> * Если таблица не существует, возвращается ошибка. Сведения о создании новой таблицы см [. в статье Создание таблицы.](create-table-command.md)

**Пример** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```