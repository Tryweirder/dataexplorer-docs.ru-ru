---
title: Kusto Инжестионбатчинг "Управление политиками" — Azure обозреватель данных
description: В этой статье описывается команда Инжестионбатчинг Policy в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ff3578b055fd487f3d339dc9b7fb4aa1ad11e14
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371447"
---
# <a name="ingestionbatching-policy-command"></a>Команда политики Инжестионбатчинг

[Политика инжестионбатчинг](batchingpolicy.md) — это объект политики, определяющий, когда должна останавливаться агрегирование данных во время приема данных в соответствии с заданными параметрами.

Для политики можно задать значение `null` . в этом случае используются значения по умолчанию, установка максимального интервала времени пакетной обработки: 5 минут, 1000 элементов и общий размер пакета (1 ГБ), а параметр кластера по умолчанию, заданный Kusto.

Если политика не задана для определенной сущности, она будет искать политику более высокого уровня иерархии, если для параметра ALL задано значение null, то будет использоваться по умолчанию. 

**Ограничения Инжестионбатчинг:**

| Тип | По умолчанию | Минимальные | Максимальная
|---|---|---|---|
| Количество элементов | 1000 | 1 | 2000 |
| Размер данных (МБ) | 1000 | 100 | 1000 |
| Время | 5 мин | 10 с | 15 минут |

## <a name="displaying-the-ingestionbatching-policy"></a>Отображение политики Инжестионбатчинг

Политику можно задать для базы данных или таблицы и отобразить с помощью одной из следующих команд:

* `.show``database`  DatabaseName `policy``ingestionbatching`
* `.show``table` *Имя_базы_данных*, `.`  TableName `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>Изменение политики Инжестионбатчинг

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

Изменение политики Инжестионбатчинг для нескольких таблиц (в одном контексте базы данных):

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

Политика Инжестионбатчинг:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type` : таблица, база данных
* `database_or_table`. Если сущность является таблицей или базой данных, ее имя должно быть указано в команде следующим образом: 
  - `database_name` или 
  - `database_name.table_name` или 
  - `table_name` (при выполнении в контексте конкретной базы данных)

## <a name="deleting-the-ingestionbatching-policy"></a>Удаление политики Инжестионбатчинг

```kusto
.delete <entity_type> <database_or_table_name> policy ingestionbatching
```

**Примеры**

```kusto
// Show IngestionBatching policy for table `MyTable` in database `MyDatabase`
.show table MyDatabase.MyTable policy ingestionbatching 

// Set IngestionBatching policy on table `MyTable` (in database context) to batch ingress data by 30 seconds, 500 files, or 1GB (whatever comes first)
.alter table MyTable policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:30", "MaximumNumberOfItems": 500, "MaximumRawDataSizeMB": 1024}'

// Set IngestionBatching policy on multiple tables (in database context) to batch ingress data by 1 minute, 20 files, or 300MB (whatever comes first)
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:01:00", "MaximumNumberOfItems": 20, "MaximumRawDataSizeMB": 300}'

// Delete IngestionBatching policy on table `MyTable`
.delete table MyTable policy ingestionbatching

// Delete IngestionBatching policy on database `MyDatabase`
.delete database MyDatabase policy ingestionbatching
```
