---
title: Управление политикой хранения Kusto в Azure обозреватель данных
description: В этой статье описывается политика хранения в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e03e529e0c802f0d424deb4048c5809bbe845ddd
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617414"
---
# <a name="retention-policy"></a>Политика хранения

В этой статье описываются управляющие команды, используемые для создания и изменения [политики хранения](retentionpolicy.md).

## <a name="show-retention-policy"></a>Отображение политики хранения

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: таблица или база данных
* `database_or_table`: `database_name` или `database_name.table_name` или `table_name` (в контексте базы данных)

**Пример**

Отображение политики хранения для базы данных с именем `MyDatabase`:

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>Удаление политики хранения

Удаление политики хранения данных влияет на неограниченное хранение данных.

Удаление политики хранения данных таблицы приведет к тому, что эта таблица будет наследовать политику хранения на уровне базы данных.

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: таблица или база данных
* `database_or_table`: `database_name` или `database_name.table_name` или `table_name` (в контексте базы данных)

**Пример**

Удалите политику хранения для таблицы с именем `MyTable1`.

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>Изменение политики хранения

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: таблица или база данных
* `database_or_table`: `database_name` или `database_name.table_name` или `table_name` (в контексте базы данных)
* `table_name`: имя таблицы в контексте базы данных.  Подстановочный знак`*` (допускается здесь).
* `retention_policy` :

```kusto
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**Примеры**

Отображение политики хранения для базы данных с именем `MyDatabase`:

```kusto
.show database MyDatabase policy retention
```

Задает политику хранения с периодом обратимого удаления в 10 дней и отключением возможности восстановления данных.

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

Задает политику хранения с периодом обратимого удаления в 10 дней и возможностью восстановления данных.

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Задает ту же политику хранения, которая указана выше, но на этот раз для нескольких таблиц (Table1, Table2 и Table3):

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Задает политику хранения со значениями по умолчанию: 100 лет в качестве периода обратимого удаления и включения возможности восстановления.

```kusto
.alter table Table1 policy retention "{}"
```