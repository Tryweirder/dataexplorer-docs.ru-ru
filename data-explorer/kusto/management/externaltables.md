---
title: Общие команды управления внешней таблицы Kusto — Azure обозреватель данных
description: В этой статье описаны общие команды управления внешними таблицами.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2020
ms.openlocfilehash: 8e419d471419a291b3680c4b91d3e6908b2e7f2e
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128841"
---
# <a name="external-table-general-control-commands"></a>Команды для общего управления внешней таблицей

Общие сведения о внешних таблицах см. в разделе [внешние таблицы](../query/schema-entities/externaltables.md) . Следующие команды относятся к _любой_ внешней таблице (любого типа).

## <a name="show-external-tables"></a>. показывать внешние таблицы

* Возвращает все внешние таблицы в базе данных (или определенную внешнюю таблицу).
* Требуется [разрешение монитора базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис** 

`.show` `external` `tables`

`.show` `external` `table` *TableName*

**Выходные данные**

| Выходной параметр | Тип   | Описание                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | строка | Имя внешней таблицы                                             |
| TableType        | строка | Тип внешней таблицы                                              |
| Папка           | строка | Папка таблицы                                                     |
| DocString        | строка | Строка документирования таблицы                                       |
| Свойства       | строка | Сериализованные свойства JSON таблицы (зависит от типа таблицы) |


**Примеры:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Папка         | DocString | Свойства |
|-----------|-----------|----------------|-----------|------------|
| T         | BLOB-объект      | екстерналтаблес | Docs      | {}         |


## <a name="show-external-table-schema"></a>. отобразить схему внешней таблицы

* Возвращает схему внешней таблицы в формате JSON или CSL. 
* Требуется [разрешение монитора базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис** 

`.show``external` `table` *TableName* `schema` `as` ( `json`  |  `csl` )

`.show` `external` `table` *TableName* `cslschema`

**Выходные данные**

| Выходной параметр | Тип   | Описание                        |
|------------------|--------|------------------------------------|
| TableName        | строка | Имя внешней таблицы            |
| схема           | строка | Схема таблицы в формате JSON |
| имя_базы_данных     | строка | Имя базы данных таблицы             |
| Папка           | строка | Папка таблицы                    |
| DocString        | строка | Строка документирования таблицы      |

**Примеры:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Выходные данные:**

*формат*

| TableName | схема    | имя_базы_данных | Папка         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Имя": "Екстерналблоб",<br>"Папка": "Екстерналтаблес",<br>"DocString": "документы",<br>"Ордередколумнс": [{"имя": "x", "Type": "System. Int64", "Кслтипе": "Long", "DocString": ""}, {"имя": "s", "Type": "System. String", "Кслтипе": "строка", "DocString": ""}]} | DB           | екстерналтаблес | Docs      |


*CSL*

| TableName | схема          | имя_базы_данных | Папка         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | КС:Лонг, с:стринг | DB           | екстерналтаблес | Docs      |

## <a name="drop-external-table"></a>. Удаление внешней таблицы

* Удаляет внешнюю таблицу 
* Невозможно восстановить определение внешней таблицы после этой операции
* Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**  

`.drop``external` `table` *TableName* [ `ifexists` ]

**Выходные данные**

Возвращает свойства удаленной таблицы. Дополнительные сведения см. в разделе [. Отображение внешних таблиц](#show-external-tables).

**Примеры:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Папка         | DocString | схема       | Свойства |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | BLOB-объект      | екстерналтаблес | Docs      | [{"Name": "x", "Кслтипе": "Long"},<br> {"Name": "s", "Кслтипе": "String"}] | {}         |

## <a name="next-steps"></a>Дальнейшие действия

* [Создание и изменение внешних таблиц в службе хранилища Azure или Azure Data Lake](external-tables-azurestorage-azuredatalake.md)
* [Создание и изменение внешних таблиц SQL](external-sql-tables.md)
