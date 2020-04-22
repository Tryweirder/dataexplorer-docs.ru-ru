---
title: Политика кэша - Проводник данных Azure (ru) Документы Майкрософт
description: В этой статье описана политика кэша в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca14703b2548bdb23dc3e6e352aeaacbc17303b4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744474"
---
# <a name="cache-policy"></a>Политика кэширования

В этой статье описаны команды, используемые для создания и изменения [политики кэша](cachepolicy.md) 

## <a name="displaying-the-cache-policy"></a>Отображение политики кэша

Политика может быть установлена на данных или таблице, и отображается с помощью одной из следующих команд:

* `.show` `database` *DatabaseName* `policy` `caching`
* `.show``table` *База данныхИмя*`.`*таблицыИмя* `policy``caching`

## <a name="altering-the-cache-policy"></a>Изменение политики кэша

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

Изменение политики кэша для нескольких таблиц (в одном контексте базы данных):

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

Политика кэша:

```kusto
{
  "DataHotSpan": {
    "Value": "3.00:00:00"
  },
  "IndexHotSpan": {
    "Value": "3.00:00:00"
  }
}
```

* `entity_type`: таблица, база данных или кластер
* `database_or_table`: если объект является таблицей или базой данных, ее имя должно быть указано в команде следующим образом - 
  - `database_name` или 
  - `database_name.table_name` или 
  - `table_name`(при запуске в контексте конкретной базы данных)

## <a name="deleting-the-cache-policy"></a>Удаляние политики кэша

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**Примеры**

Отображаем `MyTable` политику `MyDatabase`кэша для таблицы в базе данных:

```kusto
.show table MyDatabase.MyTable policy caching 
```

Настройка политики `MyTable` кэша таблицы (в контексте базы данных) до 3 дней:

```kusto
.alter table MyTable policy caching hot = 3d
```

Настройка политики для нескольких таблиц (в контексте базы данных) до 3 дней:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

Удаляем политику, установленную на столе:

```kusto
.delete table MyTable policy caching
```

Удаляем набор политики в базе данных:

```kusto
.delete database MyDatabase policy caching
```
