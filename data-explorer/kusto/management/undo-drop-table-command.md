---
title: .undo таблица падения - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается таблица отсева .undo в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 55fd08ce2624c522b971e1ee3862f7d11c6f679f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519556"
---
# <a name="undo-drop-table"></a>.undo drop table

`.undo` `drop` Команда `table` возвращает операцию таблицы сброса в конкретную версию базы данных.

**Синтаксис**

`.undo``drop` `version=v` *TableName* `as` *NewTableName* *DB_MajorVersion.DB_MinorVersion* Название таблицы - NewTableName - DB_MajorVersion.DB_MinorVersion `table`

Команда должна выполняться контекстом базы данных.

**Возвращает**

Эта команда:
* Возвращает исходный список уровней таблицы
* Определяет для каждой степени количество записей,
* Возвращается, если операция по восстановлению выполнена или не выполнена
* Возвращает причину сбоя, если это уместно.

| ExtentId                             | NumberOfRecords | Status                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | Восстановлены                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | Восстановлены                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | Не удается восстановить степень | Объем контейнера: 4b47fd84-c7db-4cfb-9378-67c1de7bf154 не был найден, степень была удалена из хранилища и не может быть восстановлена |

**Примеры**

```
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**Как найти требуемую версию базы данных**

Вы можете найти версию базы данных до `.show` `journal` того, как операция по падению была выполнена с помощью команды:

```
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| OriginalEntityVersion |
|-----------------------|
| v24.3                 |

**Ограничения**

Если команда Очистка была выполнена в этой базе данных, команда таблицы отсутчания не может быть выполнена в версии ранее для выполнения очистки.

Размер может быть восстановлен только в том случае, если период жесткого удаления контейнера, в котором он находится, еще не достигнут.

Команда требует [разрешения администрирования базы данных.](../management/access-control/role-based-authorization.md)