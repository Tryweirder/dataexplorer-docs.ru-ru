---
title: Политика кэша в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается политика кэширования в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9b080badd2dc1015319e9b6d44c4c477061f92f9
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452720"
---
# <a name="cache-policy-command"></a>команда политики кэширования

В этой статье описываются команды, используемые для создания и изменения [политики кэша](cachepolicy.md) . 

## <a name="displaying-the-cache-policy"></a>Отображение политики кэширования

Политику можно задать для базы данных, таблицы или [материализованных представлений](materialized-views/materialized-view-overview.md)и отобразить с помощью одной из следующих команд:

* `.show``database` *DatabaseName* DatabaseName `policy``caching`
* `.show``table` *TableName* TableName `policy``caching`
* `.show``materialized-view` *MaterializedViewName* Материализедвиевнаме `policy``caching`

## <a name="altering-the-cache-policy"></a>Изменение политики кэширования

```kusto
.alter <entity_type> <database_or_table_or_materialized-view_name> policy caching hot = <timespan>
```

Изменение политики кэширования для нескольких таблиц (в одном контексте базы данных):

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

* `entity_type` : таблица, база данных или кластер
* `database_or_table_or_materialized-view`. Если сущность является таблицей или базой данных, ее имя должно быть указано в команде следующим образом: 
  - `database_name` или 
  - `database_name.table_name` или 
  - `table_name` (при выполнении в контексте конкретной базы данных)

## <a name="deleting-the-cache-policy"></a>Удаление политики кэша

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**Примеры**

Отобразить политику кэширования для таблицы `MyTable` в базе данных `MyDatabase` :

```kusto
.show table MyDatabase.MyTable policy caching 
```

Задание политики кэша для таблицы `MyTable` (в контексте базы данных) до 3 дней:

```kusto
.alter table MyTable policy caching hot = 3d
.alter materialized-view MyMaterializedView policy caching hot = 3d
```

Установка политики для нескольких таблиц (в контексте базы данных) в 3 дня:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

Удаление политики, заданной для таблицы:

```kusto
.delete table MyTable policy caching
```

Удаление политики, заданной для материализованных представлений:

```kusto
.delete materialized-view MyMaterializedView policy caching
```

Удаление политики, заданной для базы данных:

```kusto
.delete database MyDatabase policy caching
```
