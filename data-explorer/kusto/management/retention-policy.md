---
title: Политика удержания - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается политика удержания в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: b0366bef619d815bbe58f91730eff70ec847c239
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520355"
---
# <a name="retention-policy"></a>Политика хранения

В этой статье описаны команды управления, используемые для создания и изменения [политики удержания.](retentionpolicy.md)

## <a name="show-retention-policy"></a>Показать политику удержания

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: таблица или база данных
* `database_or_table`: `database_name` `database_name.table_name` или `table_name` (в контексте базы данных)

**Пример**

Отображите политику `MyDatabase`удержания для базы данных под названием:

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>Удалить политику удержания

Политика удержания данных — это аффективное установление неограниченного хранения данных.

Использование политики хранения данных в таблице приведет к тому, что таблица вычеркнет политику удержания из уровня базы данных.

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: таблица или база данных
* `database_or_table`: `database_name` `database_name.table_name` или `table_name` (в контексте базы данных)

**Пример**

Удалите политику удержания `MyTable1`для таблицы с именем:

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>Изменить политику удержания

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: таблица или база данных
* `database_or_table`: `database_name` `database_name.table_name` или `table_name` (в контексте базы данных)
* `table_name`: имя таблицы в контексте базы данных.  Подстановочный знак (разрешено`*` здесь).
* `retention_policy` :

```
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**Примеры**

Отображите политику `MyDatabase`удержания для базы данных под названием:

```kusto
.show database MyDatabase policy retention
```

Устанавливает политику удержания с 10-дневным периодом мягкого удаления и отключенной возможностью восстановления данных:

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

Устанавливает политику удержания с 10-дневным периодом мягкого удаления и включенной возможностью восстановления данных:

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Устанавливает ту же политику удержания, что и выше, но на этот раз для нескольких таблиц (таблица1, таблица2 и таблица3):

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Устанавливает политику удержания значениями по умолчанию: 100 лет, как период мягкого удаления и возможность восстановления:

```kusto
.alter table Table1 policy retention "{}"
```