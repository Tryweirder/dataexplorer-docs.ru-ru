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
ms.openlocfilehash: f03a2927d3f0a4fe7ee1719594f2d1f19e231d0f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618366"
---
# <a name="alter-table-folder"></a>.alter table folder

Изменяет значение папки существующей таблицы. 

`.alter``table` *TableName* `folder` *Папка* TableName

> [!NOTE]
> * Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md)
> * [Пользователь базы данных](../management/access-control/role-based-authorization.md) , изначально создавший таблицу, также может изменять ее.
> * Если таблица не существует, возвращается ошибка. Сведения о создании новой таблицы см. в статье [Создание таблицы.](create-table-command.md)

**Примеры** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```