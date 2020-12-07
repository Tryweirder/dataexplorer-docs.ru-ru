---
title: . изменение папки таблицы в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается. изменение папки таблицы в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 8ba9eb06e07dd513d20cdade87322d9ef5f17d24
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321647"
---
# <a name="alter-table-folder"></a>.alter table folder

Изменяет значение папки существующей таблицы. 

`.alter``table` *TableName* `folder` *Папка* TableName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший таблицу, также может изменять ее.
> * Если таблица не существует, возвращается ошибка. Сведения о создании новой таблицы см. в разделе [`.create table`](create-table-command.md)

**Примеры** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```