---
title: Политика ingestionBatching - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается политика IngestionBatching в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 232767e390669a08312f10d3999d19264fb29f26
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744301"
---
# <a name="ingestionbatching-policy"></a>Политика ingestionBatching

[Политика ingestionBatching](batchingpolicy.md) — это объект политики, определяющий, когда агрегация данных должна прекратиться во время приема данных в соответствии с указанными параметрами.

Политика может быть `null`установлена на, в этом случае значения по умолчанию используются, установив максимальный промежуток времени пакетирования до: 5 минут, 1000 элементов и общий размер партии 1G или значение кластера по умолчанию, установленное Kusto.

Если политика не установлена для определенной сущности, она будет искать политику более высокого уровня иерархии, если все настроены на обнулении значения по умолчанию. 

Политика имеет нижний предел 10 секунд и не рекомендуется использовать значения, превышающее 15 минут.

## <a name="displaying-the-ingestionbatching-policy"></a>Отображение политики ingestionBatching

Политика может быть установлена в базе данных или таблице и отображаться с помощью одной из следующих команд:

* `.show` `database` *DatabaseName* `policy` `ingestionbatching`
* `.show``table` *База данныхИмя*`.`*таблицыИмя* `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>Изменение политики ingestionBatching

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

Изменение политики IngestionBatching для нескольких таблиц (в одном контексте базы данных):

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

Политика ingestionBatching:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`: таблица, база данных
* `database_or_table`: если объект является таблицей или базой данных, ее имя должно быть указано в команде следующим образом - 
  - `database_name` или 
  - `database_name.table_name` или 
  - `table_name`(при запуске в контексте конкретной базы данных)

## <a name="deleting-the-ingestionbatching-policy"></a>Удаление политики ingestionBatching

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
