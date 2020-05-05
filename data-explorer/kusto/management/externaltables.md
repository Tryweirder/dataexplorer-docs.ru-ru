---
title: Управление внешними таблицами в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается управление внешними таблицами в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: c52f0649531678e31310f5a1f4bfb97f99f15857
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741944"
---
# <a name="external-table-management"></a>Управление внешними таблицами

Общие сведения о внешних таблицах см. в разделе [внешние таблицы](../query/schema-entities/externaltables.md) . 

## <a name="common-external-tables-control-commands"></a>Команды управления общими внешними таблицами

Следующие команды относятся к _любой_ внешней таблице (любого типа).

### <a name="show-external-tables"></a>. показывать внешние таблицы

* Возвращает все внешние таблицы в базе данных (или определенную внешнюю таблицу).
* Требуется [разрешение монитора базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис** 

`.show` `external` `tables`

`.show``external` *TableName* TableName `table`

**Выходные данные**

| Выходной параметр | Type   | Описание                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | Имя внешней таблицы                                             |
| TableType        | string | Тип внешней таблицы                                              |
| Папка           | string | Папка таблицы                                                     |
| DocString        | string | Строка документирования таблицы                                       |
| Элемент Property       | string | Сериализованные свойства JSON таблицы (зависит от типа таблицы) |


**Примеры:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Папка         | DocString | Элемент Property |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | екстерналтаблес | Docs      | {}         |


### <a name="show-external-table-schema"></a>. отобразить схему внешней таблицы

* Возвращает схему внешней таблицы в формате JSON или CSL. 
* Требуется [разрешение монитора базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис** 

`.show``external` `schema` *TableName* (`json`) `table` `as`  | `csl`

`.show``external` *TableName* TableName `table``cslschema`

**Выходные данные**

| Выходной параметр | Type   | Описание                        |
|------------------|--------|------------------------------------|
| TableName        | string | Имя внешней таблицы            |
| схема           | string | Схема таблицы в формате JSON |
| имя_базы_данных     | string | Имя базы данных таблицы             |
| Папка           | string | Папка таблицы                    |
| DocString        | string | Строка документирования таблицы      |

**Примеры:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**Проверки**

*формат*

| TableName | схема    | имя_базы_данных | Папка         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Имя": "Екстерналблоб",<br>"Папка": "Екстерналтаблес",<br>"DocString": "документы",<br>"Ордередколумнс": [{"имя": "x", "Type": "System. Int64", "Кслтипе": "Long", "DocString": ""}, {"имя": "s", "Type": "System. String", "Кслтипе": "строка", "DocString": ""}]} | DB           | екстерналтаблес | Docs      |


*CSL*

| TableName | схема          | имя_базы_данных | Папка         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | КС:Лонг, с:стринг | DB           | екстерналтаблес | Docs      |

### <a name="drop-external-table"></a>. Удаление внешней таблицы

* Удаляет внешнюю таблицу 
* Невозможно восстановить определение внешней таблицы после этой операции
* Требуется [разрешение администратора базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис**  

`.drop``external` *TableName* TableName `table`

**Выходные данные**

Возвращает свойства удаленной таблицы. См. раздел [. Отображение внешних таблиц](#show-external-tables).

**Примеры:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Папка         | DocString | схема       | Элемент Property |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | екстерналтаблес | Docs      | [{"Name": "x", "Кслтипе": "Long"},<br> {"Name": "s", "Кслтипе": "String"}] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Внешние таблицы в службе хранилища Azure или Azure Data Lake

Следующая команда описывает создание внешней таблицы. Таблицу можно найти в хранилище BLOB-объектов Azure, Azure Data Lake Store Gen1 или Azure Data Lake Store Gen2. 
[Строки подключения к хранилищу](../api/connection-strings/storage.md) . Описание создания строки подключения для каждого из этих параметров. 

### <a name="create-or-alter-external-table"></a>. Create или. ALTER External Table

**Синтаксис**

`.create` | (`.alter`) `external` *TableName (* *схема*) `table`  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *Partition* [`,` ....]]  
`dataformat``=` *Формат*  
`(`  
*StorageConnectionString* [`,` ...]  
`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name* Документация] [`,` имя_папки], property_name значение... `=` `)`]

Создает или изменяет новую внешнюю таблицу в базе данных, в которой выполняется команда.

**Параметры**

* *TableName* — имя внешней таблицы. Должны соответствовать правилам для [имен сущностей](../query/schema-entities/entity-names.md). Имя внешней таблицы не может совпадать с именем обычной таблицы в той же базе данных.
* *Схема* -внешняя схема данных в формате: `ColumnName:ColumnType[, ColumnName:ColumnType ...]`. Если схема внешних данных неизвестна, используйте подключаемый модуль [infer_storage_schema](../query/inferstorageschemaplugin.md) , который может вывести схему на основе содержимого внешнего файла.
* *Partition* — одно или несколько определений секций (необязательно). См. синтаксис раздела ниже.
* *Format* — формат данных. Для запросов поддерживается любой из [форматов приема](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) . Использование внешней таблицы для [сценария экспорта](data-export/export-data-to-an-external-table.md) ограничено следующими форматами `CSV`:, `TSV`, `JSON`,. `Parquet`
* *StorageConnectionString* — один или несколько путей к контейнерам больших двоичных объектов хранилища BLOB-объектов Azure или Azure Data Lake Store файловых системах (виртуальные каталоги или папки), включая учетные данные. Дополнительные сведения см. в разделе [строки подключения к хранилищу](../api/connection-strings/storage.md) . Настоятельно рекомендуется предоставить более одной учетной записи хранения, чтобы избежать регулирования хранилища при [экспорте](data-export/export-data-to-an-external-table.md) больших объемов данных во внешнюю таблицу. При экспорте будут распределяться операции записи между всеми предоставленными учетными записями. 

**Синтаксис раздела**

[`format_datetime =` *Датетимепартитионформат*] `bin(` *Тиместампколумннаме*, *партитионбитимеспан*`)`  
|   
[*Стрингформатпрефикс*] *Стрингколумннаме* [*стрингформатсуффикс*])

**Параметры секции**

* *Датетимепартитионформат* — формат требуемой структуры каталогов в выходном пути (необязательно). Если определение секционирования определено и формат не указан, по умолчанию используется значение "гггг/мм/дд/чч/мм" на основе Партитионбитимеспан. Например, если секционировать по 1d, структура будет иметь значение "гггг/мм/дд". При секционировании по 1 ч структура будет иметь значение "гггг/мм/дд/чч".
* Столбец *тиместампколумннаме* -DateTime, по которому секционирована таблица. Столбец отметок времени должен существовать во внешнем определении схемы таблицы и выходных данных запроса экспорта при экспорте во внешнюю таблицу.
* *Партитионбитимеспан* -литерал TimeSpan, по которому следует секционировать.
* *Стрингформатпрефикс* — константный строковый литерал, который будет входить в путь к артефакту, Объединенный перед табличным значением (необязательно).
* *Стрингформатсуффикс* — константный строковый литерал, который будет входить в путь к артефакту, Объединенный после табличного значения (необязательно).
* *Стрингколумннаме* — строковый столбец, на котором секционирована таблица. Столбец String должен существовать в определении схемы внешней таблицы.

**Необязательные свойства**:

| Свойство         | Type     | Описание       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Папка таблицы                                                                     |
| `docString`      | `string` | Строка документирования таблицы                                                       |
| `compressed`     | `bool`   | Если задано, указывает, сжимаются ли большие `.gz` двоичные объекты как файлы                  |
| `includeHeaders` | `string` | Для больших двоичных объектов CSV или TSV указывает, содержат ли большие двоичные объекты заголовок.                     |
| `namePrefix`     | `string` | Если задано, указывает префикс больших двоичных объектов. При операциях записи все большие двоичные объекты будут записаны с этим префиксом. При операциях чтения считываются только большие двоичные объекты с этим префиксом. |
| `fileExtension`  | `string` | Если задано, указывает расширения файлов больших двоичных объектов. При записи имена больших двоичных объектов будут заканчиваться этим суффиксом. При чтении будут считываться только большие двоичные объекты с этим расширением файла.           |
| `encoding`       | `string` | Указывает, как текст кодируется: `UTF8NoBOM` (по умолчанию) `UTF8BOM`или.             |

Дополнительные сведения о параметрах внешней таблицы в запросах см. в разделе [логика фильтрации артефактов](#artifact-filtering-logic).

> [!NOTE]
> * Если таблица существует, `.create` команда завершится ошибкой. Используйте `.alter` для изменения существующих таблиц. 
> * Изменение схемы, формата или определения секции внешней таблицы больших двоичных объектов не поддерживается. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md) для `.create` и [разрешения администратора таблицы](../management/access-control/role-based-authorization.md) для `.alter`. 
 
**Пример** 

Внешняя таблица, не относящаяся к секционированию. Все артефакты должны находиться непосредственно под определенными контейнерами:

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
   folder = "ExternalTables"
)  
```

Внешняя таблица, секционированная по dateTime. Артефакты находятся в каталогах в формате "гггг/мм/дд" под определенными путями:

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
   folder = "ExternalTables"
)  
```

Внешняя таблица, секционированная по dateTime с форматом каталога «year = гггг/месяц = MM/Day = DD»:

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
   folder = "ExternalTables"
)
```

Внешняя таблица с ежемесячными секциями данных и форматом каталога "гггг/мм":

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
   folder = "ExternalTables"
)
```

Внешняя таблица с двумя секциями. Структура каталогов — это объединение обеих секций: форматированный CustomerName, за которым следует формат dateTime по умолчанию. Например, "CustomerName = софтворкс/2011/11/11":

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

**Выходные данные**

|TableName|TableType|Папка|DocString|Элемент Property|ConnectionStrings|Секции|
|---|---|---|---|---|---|---|
|екстерналмултиплепартитионс|Blob|екстерналтаблес|Docs|{"Format": "CSV", "сжатый": false, "Компрессионтипе": NULL, "FileExtension": "CSV", "Инклудехеадерс": "None", "Encoding": NULL, "NamePrefix": NULL}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "CustomerName ={0}", "ColumnName": "CustomerName", "Ordinal": 0}, PartitionBy ":" 1,00:00:00 "," ColumnName ":" timestamp "," Ordinal ": 1}]|

#### <a name="artifact-filtering-logic"></a>Логика фильтрации артефактов

При запросе внешней таблицы обработчик запросов отфильтровывает ненужные внешние артефакты хранилища (BLOB-объекты) для повышения производительности запросов. Ниже описывается процесс итерации больших двоичных объектов и принятие решения о необходимости обработки большого двоичного объекта.

1. Создайте шаблон URI, представляющий место, где находятся большие двоичные объекты. Изначально шаблон URI равен строке подключения, предоставленной как часть определения внешней таблицы. Если определены какие бы то ни было секции, они добавляются к шаблону URI.
Например, если строка подключения имеет значение `https://storageaccount.blob.core.windows.net/container1` и определена секция даты и времени: `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)`, соответствующий шаблон URI будет выглядеть так: `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd`, и мы будем искать большие двоичные объекты в расположениях, соответствующих этому шаблону.
Если определена дополнительная строковая Секция `"CustomerId" customerId` , соответствующий шаблон URI будет следующим: `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*`и т. д.

2. Для всех *прямых* BLOB-объектов, найденных в созданных шаблонах URI, проверьте следующее:

 * Значения секций соответствуют предикатам, используемым в запросе.
 * Имя большого двоичного `NamePrefix`объекта начинается с, если такое свойство определено.
 * Имя большого двоичного `FileExtension`объекта заканчивается на, если такое свойство определено.

После соблюдения всех условий большой двоичный объект извлекается и обрабатывается обработчиком запросов.

#### <a name="spark-virtual-columns-support"></a>Поддержка виртуальных столбцов Spark

При экспорте данных из Spark столбцы секционирования (заданные в `partitionBy` методе модуля записи данных в кадре) не записываются в файлы данных. Это делается, чтобы избежать дублирования данных, поскольку данные, уже имеющиеся в именах папок (например, `column1=<value>/column2=<value>/`), и Spark могут распознать их при чтении. Однако Kusto требует, чтобы столбцы секционирования присутствовали в самих данных. Планируется поддержка виртуальных столбцов в Kusto. До этого следует использовать следующее решение: при экспорте данных из Spark создайте копию всех столбцов, на которые секционированы данные, перед записью кадра данных.

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

При определении внешней таблицы в Kusto укажите столбцы секционирования, как в следующем примере:

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

### <a name="show-external-table-artifacts"></a>. Отображение артефактов внешней таблицы

* Возвращает список всех артефактов, которые будут обработаны при запросе данной внешней таблицы.
* Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис** 

`.show``external` *TableName* TableName `table``artifacts`

**Выходные данные**

| Выходной параметр | Type   | Описание                       |
|------------------|--------|-----------------------------------|
| URI              | string | URI внешнего артефакта хранилища |

**Примеры:**

```kusto
.show external table T artifacts
```

**Проверки**

| URI                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

### <a name="create-external-table-mapping"></a>. Создание сопоставления внешней таблицы

`.create``external` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* Екстерналтабленаме `json` маппингинжсонформат `table`

Создает новое сопоставление. Дополнительные сведения см. в разделе [сопоставления данных](./mappings.md#json-mapping).

**Пример** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Пример выходных данных**

| name     | Вид | Сопоставление                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "int", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"путь": "$. ROWGUID"}}] |

### <a name="alter-external-table-mapping"></a>. изменение сопоставления внешней таблицы

`.alter``external` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* Екстерналтабленаме `json` маппингинжсонформат `table`

Изменяет существующее сопоставление. 
 
**Пример** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Пример выходных данных**

| name     | Вид | Сопоставление                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"путь": "$. ROWGUID"}}] |

### <a name="show-external-table-mappings"></a>. Отображение сопоставлений внешних таблиц

`.show``external` `mapping` *MappingName* *ExternalTableName* Екстерналтабленаме MappingName `table` `json` 

`.show``external` `json` *ExternalTableName* Екстерналтабленаме `table``mappings`

Отображение сопоставлений (все или указанное по имени).
 
**Пример** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Пример выходных данных**

| name     | Вид | Сопоставление                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"путь": "$. ROWGUID"}}] |

### <a name="drop-external-table-mapping"></a>. Удаление сопоставления внешней таблицы

`.drop``external` `mapping` *MappingName* *ExternalTableName* Екстерналтабленаме MappingName `table` `json` 

Удаляет сопоставление из базы данных.
 
**Пример** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>Внешняя таблица SQL

### <a name="create-or-alter-external-sql-table"></a>. Создание или изменение внешней таблицы SQL

Создает или изменяет внешнюю таблицу SQL в базе данных, в которой выполняется команда.  

**Синтаксис**

`.create` | (`.alter`) `external` TableName ([ColumnName: columnType],...) *TableName* `table`  
`kind` `=` `sql`  
`table``=` *Склтабленаме*  
`(`*склсерверконнектионстринг*`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name* Документация] [`,` имя_папки], property_name значение... `=` `)`]


**Вход**

* *TableName* — имя внешней таблицы. Должны соответствовать правилам для [имен сущностей](../query/schema-entities/entity-names.md). Имя внешней таблицы не может совпадать с именем обычной таблицы в той же базе данных.
* *Склтабленаме* — имя таблицы SQL.
* *Склсерверконнектионстринг* — строка подключения к SQL Server. Может принимать следующие значения: 
    * **Встроенная проверка подлинности AAD** (`Authentication="Active Directory Integrated"`): пользователь или приложение проходят проверку подлинности с помощью AAD в Kusto, и тот же токен используется для доступа к конечной точке сети SQL Server.
    * **Проверка подлинности имени пользователя и пароля** (`User ID=...; Password=...;`). Если внешняя таблица используется для [непрерывного экспорта](data-export/continuous-data-export.md), необходимо выполнить проверку подлинности с помощью этого метода. 

> [!WARNING]
> Строки подключения и запросы, содержащие конфиденциальные сведения, должны быть замаскированы, чтобы их можно было опустить из любой трассировки Kusto. Дополнительные сведения см. в разделе [немаскированные строковые литералы](../query/scalar-data-types/string.md#obfuscated-string-literals) .

**Необязательные свойства**
| Свойство            | Type            | Описание                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Папка таблицы.                  |
| `docString`         | `string`        | Строка, задокументированная в таблице.      |
| `firetriggers`      | `true`/`false`  | Значение `true`указывает, что целевая система должна срабатывать триггеры INSERT, определенные в таблице SQL. Значение по умолчанию — `false`. (Дополнительные сведения см. в разделе [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) и [System. Data. SqlClient. SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)). |
| `createifnotexists` | `true`/ `false` | Если `true`значение равно, то Целевая таблица SQL будет создана, если она еще не существует. в `primarykey` этом случае необходимо указать свойство для указания результирующего столбца, который является первичным ключом. Значение по умолчанию — `false`.  |
| `primarykey`        | `string`        | Если `createifnotexists` имеет `true`значение, то указывает имя столбца в результатах, который будет использоваться в качестве ПЕРВИЧного ключа таблицы SQL, если он создан с помощью этой команды.                  |

> [!NOTE]
> * Если таблица существует, `.create` команда завершится ошибкой. Используйте `.alter` для изменения существующих таблиц. 
> * Изменение схемы или формата внешней таблицы SQL не поддерживается. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md) для `.create` и [разрешения администратора таблицы](../management/access-control/role-based-authorization.md) для `.alter`. 
 
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

**Выходные данные**

| TableName   | TableType | Папка         | DocString | Элемент Property                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| екстерналскл | SQL       | екстерналтаблес | Docs      | {<br>  "Таржетентитикинд": "склтабле",<br>  "Таржетентитинаме": "Мисклтабле",<br>  "Таржетентитиконнектионстринг": "Server = TCP:мисервер. Database. Windows. NET, 1433; Authentication = Active Directory интегрирован; исходный каталог = MyDatabase; ",<br>  "Фиретригжерс": true,<br>  "CreateIfNotExists": true,<br>  "PrimaryKey": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>Запрос к внешней таблице типа SQL 
Аналогично для других типов внешних таблиц, поддерживается запрос к внешней таблице SQL. См. раздел [запросы к внешним таблицам](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data). Обратите внимание, что реализация запроса к внешней таблице SQL будет выполнять полную инструкцию "SELECT *" (или выбрать соответствующие столбцы) из таблицы SQL, а остальная часть запроса будет выполняться на Kusto стороне. Рассмотрим следующий запрос к внешней таблице: 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto будет выполнять запрос SELECT * из таблицы в базе данных SQL, за которым следует счетчик на стороне Kusto. В таких случаях производительность должна быть лучше, если написать непосредственно в T-SQL напрямую ("SELECT COUNT (1) из таблицы") и выполнить с помощью [подключаемого модуля sql_request](../query/sqlrequestplugin.md), а не с помощью функции внешней таблицы. Аналогичным образом фильтры не отправляются в SQL запрос.  
Рекомендуется использовать внешнюю таблицу для запроса таблицы SQL, если запрос требует считывания всей таблицы (или соответствующих столбцов) для дальнейшего выполнения на стороне Kusto. Если SQL-запрос может быть значительно оптимизирован в языке T SQL, используйте [подключаемый модуль sql_request](../query/sqlrequestplugin.md).
