---
title: . Перетащите Теги экстента в Azure обозреватель данных
description: В этой статье описывается команда DROP экстент Tags в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 45e7d0abf42e613a9d197371dcc374fe4ac11fed
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321018"
---
# <a name="drop-extent-tags"></a>.drop extent tags

Команда выполняется в контексте определенной базы данных. Он удаляет определенные [теги экстентов](extents-overview.md#extent-tagging) из всех или конкретных экстентов в базе данных и таблице.  

> [!NOTE]
> Сегменты данных называются **экстентами** в Kusto, а все команды используют "экстент" или "экстенты" в качестве синонима.
> Дополнительные сведения о экстентах см. в статье [Общие сведения о экстентах (сегментах данных)](extents-overview.md).

Существует два способа указать, какие теги следует удалить, из каких экстентов:

* Явно укажите теги, которые должны быть удалены из всех экстентов в указанной таблице.
* Укажите запрос, результаты которого указывают идентификаторы экстентов в таблице, и для каждого экстента — теги, которые следует удалить.

## <a name="syntax"></a>Синтаксис

`.drop` [ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` ' *Тагн*"]`)`

`.drop`[ `async` ] `extent` `tags`  <|  *запрос*

`async` (необязательно): асинхронное выполнение команды.
   * Возвращается идентификатор операции (GUID).
   * Состояние операции можно отслеживать. Используйте [`.show operations`](operations.md#show-operations) команду.
   * Используйте [`.show operation details`](operations.md#show-operation-details) команду, чтобы получить результаты успешного выполнения.

## <a name="restrictions"></a>Ограничения

Все экстенты должны находиться в базе данных контекста и должны принадлежать к одной и той же таблице.

## <a name="specify-extents-with-a-query"></a>Указание экстентов с помощью запроса

Экстенты и теги для удаления указываются с помощью запроса Kusto. Он возвращает набор записей со столбцом с именем «Екстентид» и столбцом «Tagss».

> [!NOTE]
> При использовании [клиентской библиотеки Kusto .NET](../api/netfx/about-kusto-data.md)в следующих методах создается необходимая команда:
> * `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
> * `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

Требуется [разрешение администратора таблицы](../management/access-control/role-based-authorization.md) для всех используемых исходных и целевых таблиц.

### <a name="syntax-for-drop-extent-tags-in-query"></a>Синтаксис для тегов области перетаскивания в запросе

```kusto 
.drop extent tags <| ...query...
```

### <a name="return-output"></a>Возврат выходных данных

Выходной параметр |Тип |Описание 
---|---|---
оригиналекстентид |строка |Уникальный идентификатор (GUID) для исходного экстента, теги которого были изменены. Экстент удаляется как часть операции.
ресултекстентид |строка |Уникальный идентификатор (GUID) экстента результатов, в котором были изменены Теги. Экстент создается и добавляется как часть операции. При сбое — "Failed".
ресултекстенттагс |строка |Коллекция тегов, с которыми помечается экстент результата, если они существуют, или значение NULL в случае сбоя операции.
Сведения |строка |Включает сведения об ошибке в случае сбоя операции.

## <a name="examples"></a>Примеры

### <a name="drop-one-tag"></a>Удалить один тег

Удалите `drop-by:Partition000` тег из любого экстента в таблице, помеченной как.

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

### <a name="drop-several-tags"></a>Удалить несколько тегов

Удалите теги `drop-by:20160810104500` , `a random tag` и `drop-by:20160810` из любого экстента в таблице, помеченной любым из них:

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

### <a name="drop-all-drop-by-tags"></a>Удалить все `drop-by` Теги 

Удалить все `drop-by` теги из экстентов в таблице `MyTable` :

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

### <a name="drop-all-tags-matching-specific-regex"></a>Удалить все теги, соответствующие конкретному регулярному выражению 

Удалить все теги, соответствующие регулярному выражению, `drop-by:StreamCreationTime_20160915(\d{6})` из экстентов в таблице `MyTable` :

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

## <a name="sample-output"></a>Пример выходных данных

|оригиналекстентид |ресултекстентид | ресултекстенттагс | Сведения
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |
