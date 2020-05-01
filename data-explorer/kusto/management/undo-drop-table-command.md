---
title: . отменить удаление таблицы в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описано, как отменить удаление таблицы в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2682afaa9e589a8787b7e42a83e3bb68a24a2781
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616683"
---
# <a name="undo-drop-table"></a>.undo drop table

`.undo` операцию Drop Table к определенной версии базы `drop` `table` данных.

**Синтаксис**

`.undo``drop` `as` *NewTableName* *TableName* `version=v` *DB_MajorVersion.DB_MinorVersion* TableName [невтабленаме] DB_MajorVersion. DB_MinorVersion `table`

Команда должна выполняться с контекстом базы данных.

**Возвращает**

Эта команда:
* Возвращает список экстентов исходной таблицы
* Задает для каждого экстента количество записей, содержащихся в экстенте.
* Возвращает значение, если операция восстановления завершилась успешно или с ошибкой
* Возвращает причину сбоя, если это уместно.

| екстентид                             | нумберофрекордс | Состояние                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | Йдет                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | Йдет                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | Не удалось восстановить экстент | Контейнер экстента: 4b47fd84-c7db-4cfb-9378-67c1de7bf154 не найден, экстент удален из хранилища и не может быть восстановлен |

**Примеры**

```kusto
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```kusto
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**Как найти требуемую версию базы данных**

Версию базы данных можно найти до выполнения операции DROP с помощью `.show` `journal` команды:

```kusto
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| оригиналентитиверсион |
|-----------------------|
| v 24.3                 |

**Ограничения**

Если в этой базе данных была выполнена команда "очистить", команда "отменить удаление таблицы" не может быть выполнена до версии, предшествующей выполнению очистки.

Экстент можно восстановить только в том случае, если не достигнут период жесткого удаления контейнера экстентов, в котором он находится.

Команде требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md).
