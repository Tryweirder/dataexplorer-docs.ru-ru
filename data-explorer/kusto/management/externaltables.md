---
title: Управление внешней таблицей - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается управление внешней таблицей в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 680a4e25d6b478fe171aa3296de81c0106417877
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744707"
---
# <a name="external-table-management"></a>Управление внешней таблицей

Ознакомьтесь с [внешними таблицами](../query/schema-entities/externaltables.md) для обзора внешних таблиц. 

## <a name="common-external-tables-control-commands"></a>Общие команды управления внешними таблицами

Следующие команды имеют отношение к _любой_ внешней таблице (любого типа).

### <a name="show-external-tables"></a>.show внешние таблицы

* Возвращает все внешние таблицы в базе данных (или конкретную внешнюю таблицу).
* Требуется [разрешение мониторинга базы данных.](../management/access-control/role-based-authorization.md)

**Синтаксис** 

`.show` `external` `tables`

`.show``external` *таблицы* `table`

**Вывод**

| Выходной параметр | Тип   | Описание                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | строка | Название внешней таблицы                                             |
| TableType        | строка | Тип внешней таблицы                                              |
| Папка           | строка | Папка таблицы                                                     |
| DocString        | строка | Строка документирования таблицы                                       |
| Свойства       | строка | Серийные свойства таблицы JSON (специфические к типу таблицы) |


**Примеры:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Папка         | DocString | Свойства |
|-----------|-----------|----------------|-----------|------------|
| T         | BLOB-объект      | Внешниетаблицы | Docs      | {}         |


### <a name="show-external-table-schema"></a>.show внешняя схема таблицы

* Возвращает схему внешней таблицы, как JSON или CSL. 
* Требуется [разрешение мониторинга базы данных.](../management/access-control/role-based-authorization.md)

**Синтаксис** 

`.show``external` `as` `json` |  *TableName* `schema` ТаблицаИмя`csl`() `table`

`.show``external` *таблицы* `table``cslschema`

**Вывод**

| Выходной параметр | Тип   | Описание                        |
|------------------|--------|------------------------------------|
| TableName        | строка | Название внешней таблицы            |
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

**Выход:**

*Json:*

| TableName | схема    | имя_базы_данных | Папка         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | "Имя":"ВнешнийБлоб",<br>"Folder":"Внешние таблицы",<br>"DocString":"Документы",<br>"Заказанные колонки": "Name":"x","Type":"System.Int64","CslType":"длинный","DocString":"""Имя":"Имя":"А","Тип":"Система","CslType":"Строка","DocString":"За" | DB           | Внешниетаблицы | Docs      |


*Csl:*

| TableName | схема          | имя_базы_данных | Папка         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:длинный,s:string | DB           | Внешниетаблицы | Docs      |

### <a name="drop-external-table"></a>.drop внешняя таблица

* Капли внешней таблицы 
* Определение внешней таблицы не может быть восстановлено после этой операции
* Требуется [разрешение админа базы данных.](../management/access-control/role-based-authorization.md)

**Синтаксис**  

`.drop``external` *таблицы* `table`

**Вывод**

Возвращает свойства упавной таблицы. Смотрите [внешние таблицы .show](#show-external-tables).

**Примеры:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Папка         | DocString | схема       | Свойства |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | BLOB-объект      | Внешниетаблицы | Docs      | "Имя": "x", "CslType": "длинный",<br> "Имя": "s", "CslType": "строка" | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Внешние таблицы в Azure Storage или Azure Data Lake

Следующая команда описывает, как создать внешнюю таблицу. Таблица может быть размещена в Хранилище Azure Blob, магазине Azure Data Lake Store Gen1 или Магазине озера данных Azure Data. Gen2. 
[Строки подключения хранилища](../api/connection-strings/storage.md) описывают создание строки соединения для каждого из этих параметров. 

### <a name="create-or-alter-external-table"></a>.create или .alter внешняя таблица

**Синтаксис**

`.create` | `.alter`() `external` *Schema*TableName ( Схема ) *TableName* `table`  
`kind` `=` (`blob` | `adl`)  
`partition` `by` *Разделить* .`,` . . . . . . . . . . . . . . . . . . . . . . . . .  
`dataformat``=` *Формат*  
`(`  
*StorageConnectionString* `,`  
`)`  
`with` `(`Документация`docstring` `folder` *Documentation* `=` *value*`,` *property_name* - `=` *FolderName*FolderName, property_name значение ...`,` `=` `)`]

Создает или изменяет новую внешнюю таблицу в базе данных, в которой выполняется команда.

**Параметры**

* *TableName* - Название внешнего стола. Должны следовать правилам для [имен сущности.](../query/schema-entities/entity-names.md) Внешняя таблица не может иметь то же имя, что и обычная таблица в той же базе данных.
* *Схема* - Внешняя схема данных `ColumnName:ColumnType[, ColumnName:ColumnType ...]`в формате: . Если внешняя схема данных неизвестна, используйте [infer_storage_schema](../query/inferstorageschemaplugin.md) плагин, который может сделать вывод о схеме на основе содержимого внешнего файла.
* *Раздел* - Одно или несколько определений раздела (по желанию). Ниже приведен синтаксис раздела.
* *Формат* - Формат данных. Любой из [форматов приема](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) поддерживается для запроса. Использование внешней таблицы для [экспортного](data-export/export-data-to-an-external-table.md) сценария `CSV` `TSV`ограничено следующими форматами: , , `JSON` `Parquet`.
* *StorageConnectionString* - Один или несколько путей к контейнерам Azure Blob Storage blob или файловым системам Azure Data Lake Store (виртуальные каталоги или папки), включая учетные данные. Подробнее о [строках подключения к хранилищу.](../api/connection-strings/storage.md) Настоятельно рекомендуется предоставить более одной учетной записи хранилища, чтобы избежать регулирования хранилища при [экспорте](data-export/export-data-to-an-external-table.md) больших объемов данных во внешнюю таблицу. Экспорт будет распределять записи между всеми предоставленными счетами. 

**Синтаксис раздела**

-`format_datetime =` *DateTimePartitionFormat* `bin(` *TimestampColumnName*, *РазделByTimeSpan*`)`  
|   
-*StringFormatPrefix* *СтрокаКолпаНамик -* *StringFormatSuffix*)

**Параметры раздела**

* *DateTimePartitionFormat* - формат требуемой структуры каталога в пути вывода (необязательно). Если раздел определен и формат не указан, по умолчанию по умолчанию является "yyyy/MM/dd/HH/mm", на основе разделаByTimeSpan. Например, если вы разделите на 1d, структура будет "yyyy/MM/dd". Если вы разделите на 1h, структура будет "yyyy / ММ / DD / HH".
* *TimestampColumnName* - колонка времени для дат, по которой разделена таблица. Столбец timestamp должен существовать во внешнем определении схемы таблицы и выходе экспортного запроса при экспорте во внешнюю таблицу.
* *РазделByTimeSpan* - Timespan буквально, с помощью которого раздел.
* *StringFormatPrefix* - постоянная строка буквально, которая будет частью пути артефакта, сконструированного перед значением таблицы (необязательно).
* *StringFormatSuffix* - постоянная строка буквально, которая будет частью пути артефакта, concatenated после таблицы значение (необязательно).
* *StringColumnName* - строка столбца, по которой разделена таблица. Строка столбца должна существовать в определении внешней схемы таблицы.

**Необязательные свойства**:

| Свойство         | Тип     | Описание       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Папка таблицы                                                                     |
| `docString`      | `string` | Строка документирования таблицы                                                       |
| `compressed`     | `bool`   | Если установлен, указывается, сжимаются ли капли в виде `.gz` файлов                  |
| `includeHeaders` | `string` | Для CSV или TSV капли, указывает ли капли содержат заголовок                     |
| `namePrefix`     | `string` | Если установлен, указывает префикс капли. На записи операций, все капли будут написаны с этой приставкой. При чтении операции, только капли с этой приставкой считываются. |
| `fileExtension`  | `string` | Если набор, указывает на расширение файла капли. На писать, капли имена закончится с этим суффиксом. На читайте, только капли с этим расширением файла будут читаться.           |
| `encoding`       | `string` | Указывает, как закодирован `UTF8NoBOM` текст: (по умолчанию) или `UTF8BOM`.             |

> [!NOTE]
> * Если таблица `.create` существует, команда выйдет из строя с ошибкой. Используйте `.alter` для изменения существующих таблиц. 
> * Изменение схемы, формата или определения раздела внешней таблицы каплей не поддерживается. 

Требуется разрешение `.create` пользователя базы [данных](../management/access-control/role-based-authorization.md) для `.alter`и разрешения [администрирования таблицы](../management/access-control/role-based-authorization.md) для . 
 
**Пример** 

Внешняя таблица, не разделенная. Все артефакты, как ожидается, будут находиться непосредственно под контейнером (ы) определены:

```kusto
.create external table ExternalBlob (x:long, s:string) 
kind=blob
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

Внешняя таблица, разделенная dateTime. Артефакты находятся в каталогах в формате "yyyy/MM/dd" под траекторией (ы) определены:

```kusto
.create external table ExternalAdlGen2 (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by bin(Timestamp, 1d)
dataformat=csv
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

Внешняя таблица, разделенная dateTime с форматом каталога "year'yyyy/month/MM/day-dd":

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="'year='yyyy/'month='MM/'day='dd" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

Внешняя таблица с ежемесячными разделами данных и форматом каталога "yyyy/MM":

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="yyyy/MM" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

Внешняя таблица с двумя разделами. Структура каталога представляет собой конкатацию обоих разделов: отформатированное CustomerName с по следующим образом форматом dateTime по умолчанию. Например, "CustomerName-softworks/2011/11/11":

```kusto
.create external table ExternalMultiplePartitions (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"   
)
```

**Вывод**

|TableName|TableType|Папка|DocString|Свойства|ConnectionStrings|Секции|
|---|---|---|---|---|---|---|
|ВнешнеенееНесколькоразделов|BLOB-объект|Внешниетаблицы|Docs|"Формат":"Csv","Compressed":false,"CompressionType":null,"FileExtension":"CSV","IncludeHeaders":"Никто","Кодирование":null,"NamePrefix":null|["https://storageaccount.blob.core.windows.net/container1;*******"]}|"StringFormat":"CustomerName","ColumnName":"CustomerName","Ordinal":0,PartitionBy":"1.00:00:00","ColumnName":"Timestamp","Ordinal":1{0}|

#### <a name="spark-virtual-columns-support"></a>Поддержка виртуальных столбцов Spark

При экспорте данных из Spark столбцы разделов (указанные в `partitionBy` методе автора данных) не записываются в файлы данных. Это делается для того, чтобы избежать дублирования данных, поскольку данные, уже присутствующие в именах "папки" (например, `column1=<value>/column2=<value>/`), и Spark могут распознать их при чтении. Однако Kusto требует, чтобы столбцы разделов присутствовали в самих данных. Планируется поддержка виртуальных столбцов в Кусто. До тех пор используйте следующий обходной путь: при экспорте данных из Spark создайте копию всех столбцов, которые данные разделены перед написанием кадра данных:

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

При определении внешней таблицы в Кусто укажите столбцы разделов, как в следующем примере:

```kusto
.create external table ExternalSparkTable(a:string, b:datetime) 
kind=blob
partition by 
   "_a="a,
   format_datetime="'_b='yyyyMMdd" bin(b, 1d)
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

### <a name="show-external-table-artifacts"></a>.show внешние артефакты таблицы

* Возвращает список всех артефактов, которые будут обработаны при запросе данной внешней таблицы.
* Требуется [разрешение пользователя базы данных.](../management/access-control/role-based-authorization.md)

**Синтаксис** 

`.show``external` *таблицы* `table``artifacts`

**Вывод**

| Выходной параметр | Тип   | Описание                       |
|------------------|--------|-----------------------------------|
| URI              | строка | URI внешнего артефакта хранения |

**Примеры:**

```kusto
.show external table T artifacts
```

**Выход:**

| URI                                                                     |
|-------------------------------------------------------------------------|
| https://storageaccount.blob.core.windows.net/container1/folder/file.csv |

### <a name="create-external-table-mapping"></a>.создать внешнее отображение таблицы

`.create``external` *ExternalTableName* `json` `mapping` *MappingName* Внешнее имя Картографическое *имя КартпингинжонФормат* `table`

Создает новое отображение. Для получения дополнительной [информации см.](./mappings.md#json-mapping)

**Пример** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Вывод примера**

| Имя     | Вид | Сопоставление                                                           |
|----------|------|-------------------------------------------------------------------|
| отображение1 | JSON | «ColumnName»: «rownumber»:»,ColumnType»:«Int»,«Свойства»: «Путь»:«Путь»:«$.rownumber», «ColumnName»: «Rowguid»,«ColumnType»:««Свойства»: «Путь»:«$.....rowguid» |

### <a name="alter-external-table-mapping"></a>.alter внешнее отображение таблицы

`.alter``external` *ExternalTableName* `json` `mapping` *MappingName* Внешнее имя Картографическое *имя КартпингинжонФормат* `table`

Изменяет существующее отображение. 
 
**Пример** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Вывод примера**

| Имя     | Вид | Сопоставление                                                                |
|----------|------|------------------------------------------------------------------------|
| отображение1 | JSON | «ColumnName»: «rownumber»:»,ColumnType»:«,«Свойства»: «Путь»:«Путь»:«$.rownumber», «ColumnName»: «Rowguid»,«ColumnType»:«,«Свойства»: «Путь»:«$.rowguid» |

### <a name="show-external-table-mappings"></a>.show внешние отображения таблиц

`.show``external` `json` `mapping` *MappingName* *ExternalTableName* ВнешнийTableName MappingName `table` 

`.show``external` `json` ВнешнийTableName *ExternalTableName* `table``mappings`

Отображение карт (все или тот, указанный по имени).
 
**Пример** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Вывод примера**

| Имя     | Вид | Сопоставление                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| отображение1 | JSON | «ColumnName»: «rownumber»:»,ColumnType»:«,«Свойства»: «Путь»:«Путь»:«$.rownumber», «ColumnName»: «Rowguid»,«ColumnType»:«,«Свойства»: «Путь»:«$.rowguid» |

### <a name="drop-external-table-mapping"></a>.drop внешнее отображение таблицы

`.drop``external` `json` `mapping` *MappingName* *ExternalTableName* ВнешнийTableName MappingName `table` 

Выпадает отображение из базы данных.
 
**Пример** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>Внешняя таблица СЗЛ

### <a name="create-or-alter-external-sql-table"></a>.создать или изменить внешнюю таблицу sql

Создает или изменяет внешнюю таблицу S'L в базе данных, в которой выполняется команда.  

**Синтаксис**

`.create` | `.alter`() `external` TableName («columnName»:columnType»,,...) *TableName* `table`  
`kind` `=` `sql`  
`table``=` *SqlTableName*  
`(`*SqlServerConnectionString*`)`  
`with` `(`Документация`docstring` `folder` *Documentation* `=` *value*`,` *property_name* - `=` *FolderName*FolderName, property_name значение ...`,` `=` `)`]


**Параметры**

* *TableName* - Название внешнего стола. Должны следовать правилам для [имен сущности.](../query/schema-entities/entity-names.md) Внешняя таблица не может иметь то же имя, что и обычная таблица в той же базе данных.
* *SqlTableName* - Название таблицы S'L.
* *SqlServerConnectionString* - Строка подключения к серверу S'L. Может применяться один из перечисленных ниже типов. 
    * **AAD интегрировала аутентификацию** (`Authentication="Active Directory Integrated"`): Пользователь или приложение аутентифицирует через AAD к Кусто, и тот же маркер затем используется для доступа к конечной точке сети S'L Server.
    * **Имя пользователя/проверка подлинности паролей** ().`User ID=...; Password=...;` Если внешняя таблица используется для [непрерывного экспорта,](data-export/continuous-data-export.md)проверка подлинности должна быть выполнена с помощью этого метода. 

> [!WARNING]
> Строки подключения и запросы, которые включают конфиденциальную информацию, должны быть запутываны, чтобы они были исключены из любого отслеживания Kusto. Для получения дополнительной информации смотрите [запутанные буквы строки.](../query/scalar-data-types/string.md#obfuscated-string-literals)

**Дополнительные свойства**
| Свойство            | Тип            | Описание                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Папка стола.                  |
| `docString`         | `string`        | Строка, документирующая таблицу.      |
| `firetriggers`      | `true`/`false`  | Если `true`, поручает целевой системе для запуска триггеров INSERT, определенных на таблице S'L. Значение по умолчанию — `false`. (Для получения дополнительной информации [см. BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) и [System.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)) |
| `createifnotexists` | `true`/ `false` | Если `true`целевая таблица S'L будет создана, если она еще не существует; свойство `primarykey` должно быть предоставлено в этом случае, чтобы указать столбец результата, который является основным ключом. Значение по умолчанию — `false`.  |
| `primarykey`        | `string`        | Если `createifnotexists` `true`есть, указывается имя столбца в результате, которое будет использоваться в качестве основного ключа таблицы S'L, если оно создано этой командой.                  |

> [!NOTE]
> * Если таблица существует, `.create` команда выйдет из строя с ошибкой. Используйте `.alter` для изменения существующих таблиц. 
> * Изменение схемы или формата внешней таблицы S'L не поддерживается. 

Требуется разрешение `.create` пользователя базы [данных](../management/access-control/role-based-authorization.md) для `.alter`и разрешения [администрирования таблицы](../management/access-control/role-based-authorization.md) для . 
 
**Пример** 

```kusto
.create external table ExternalSql (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**Вывод**

| TableName   | TableType | Папка         | DocString | Свойства                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| ВнешнийSql | SQL       | Внешниетаблицы | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString": "Сервер:myserver.database.windows.net,1433; Аутентификация-Активный каталог Интегрированный;Первоначальный каталог»-моя база данных;",<br>  "FireTriggers": правда,<br>  "CreateifNotExists": правда,<br>  "PrimaryKey": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>Запрос навнешней таблицы типа S'L 
По аналогии с другими типами внешних таблиц поддерживается запрос на внешнюю таблицу S'L. Смотрите [запрос внешних таблиц](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data). Обратите внимание, что реализация внешнего запроса таблицы S'L выполняет полный 'SELECT q' (или выберите соответствующие столбцы) из таблицы S'L, в то время как остальная часть запроса будет выполняться на стороне Kusto. Рассмотрим следующий внешний настольный запрос: 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto выполнит запрос 'SELECT - от TABLE' к базе данных S'L, а затем будет рассчитывать на сторону Кусто. В таких случаях производительность, как ожидается, будет лучше, если писать в T-S'L непосредственно ('SELECT COUNT (1) FROM TABLE') и выполняться с использованием [sql_request плагина,](../query/sqlrequestplugin.md)вместо того, чтобы использовать функцию внешней таблицы. Аналогичным образом, фильтры не перемещаются в запрос S'L.  
Рекомендуется использовать внешнюю таблицу для запроса таблицы S'L, когда запрос требует прочтения всей таблицы (или соответствующих столбцов) для дальнейшего выполнения на стороне Кусто. При значительной оптимизации запроса на S-L в T-S'L используйте [sql_request плагин.](../query/sqlrequestplugin.md)
