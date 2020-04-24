---
title: Папка таблицы .alter - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описана папка таблицы .alter в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: a1d368d50f0e42acdbc25348b8025fbe8b530536
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522310"
---
# <a name="alter-table-folder"></a>.alter table folder

Изменяет значение Folder существующей таблицы. 

`.alter``table` *ТаблицаИмя* `folder` *Фолдер*

> [!NOTE]
> * Требуется [разрешение администрирования администрирования базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных,](../management/access-control/role-based-authorization.md) который первоначально создал таблицу, также может отсеить ее
> * Если таблица не существует, возвращается ошибка. Для создания новой таблицы [см.](create-table-command.md)

**Примеры** 

```
.alter table MyTable folder "Updated folder"
```

```
.alter table MyTable folder @"First Level\Second Level"
```