---
title: Внешние таблицы в службе хранилища Azure или Azure Data Lake Azure обозреватель данных
description: В этой статье описывается управление внешними таблицами в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: db99d1d46c321bff0f5d7b370766900ea7d1d5a0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227729"
---
# <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Внешние таблицы в службе хранилища Azure или Azure Data Lake

Следующая команда описывает создание внешней таблицы. Таблицу можно найти в хранилище BLOB-объектов Azure, Azure Data Lake Store Gen1 или Azure Data Lake Store Gen2. 
[Строки подключения к хранилищу](../api/connection-strings/storage.md) . Описание создания строки подключения для каждого из этих параметров. 

## <a name="create-or-alter-external-table"></a>. Create или. ALTER External Table

**Синтаксис**

( `.create`  |  `.alter` ) `external` `table` *TableName* (*схема*)  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` *Partition* [ `,` ....]]  
`dataformat``=` *Формат*  
`(`  
*StorageConnectionString* [ `,` ...]  
`)`  
[ `with` `(` [ `docstring` `=` *Документация*] [ `,` `folder` `=` *имя_папки*], *property_name* `=` *значение* `,` ... `)` ]

Создает или изменяет новую внешнюю таблицу в базе данных, в которой выполняется команда.

**Параметры**

* *TableName* — имя внешней таблицы. Должны соответствовать правилам для [имен сущностей](../query/schema-entities/entity-names.md). Имя внешней таблицы не может совпадать с именем обычной таблицы в той же базе данных.
* *Схема* -внешняя схема данных в формате: `ColumnName:ColumnType[, ColumnName:ColumnType ...]` . Если схема внешних данных неизвестна, используйте подключаемый модуль [infer_storage_schema](../query/inferstorageschemaplugin.md) , который может вывести схему на основе содержимого внешнего файла.
* *Partition* — одно или несколько определений секций (необязательно). См. синтаксис раздела ниже.
* *Format* — формат данных. Для запросов поддерживается любой из [форматов приема](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) . Использование внешней таблицы для [сценария экспорта](data-export/export-data-to-an-external-table.md) ограничено следующими форматами: `CSV` , `TSV` , `JSON` , `Parquet` .
* *StorageConnectionString* — один или несколько путей к контейнерам больших двоичных объектов хранилища BLOB-объектов Azure или Azure Data Lake Store файловых системах (виртуальные каталоги или папки), включая учетные данные. Дополнительные сведения см. в разделе [строки подключения к хранилищу](../api/connection-strings/storage.md) . Предоставьте более одной учетной записи хранения, чтобы избежать регулирования хранилища при [экспорте](data-export/export-data-to-an-external-table.md) больших объемов данных во внешнюю таблицу. При экспорте будут распределяться операции записи между всеми предоставленными учетными записями. 

**Синтаксис раздела**

[ `format_datetime =` *Датетимепартитионформат*] `bin(` *Тиместампколумннаме*, *партитионбитимеспан*`)`  
|   
[*Стрингформатпрефикс*] *Стрингколумннаме* [*стрингформатсуффикс*])

**Параметры секции**

* *Датетимепартитионформат* — формат требуемой структуры каталогов в выходном пути (необязательно). Если определено секционирование и формат не указан, по умолчанию используется значение гггг/мм/дд/чч/мм. Этот формат основан на Партитионбитимеспан. Например, если секционировать по 1d, структура будет иметь значение "гггг/мм/дд". При секционировании по 1 h структура будет иметь значение "гггг/мм/дд/чч".
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
| `compressed`     | `bool`   | Если задано, указывает, сжимаются ли большие двоичные объекты как `.gz` файлы                  |
| `includeHeaders` | `string` | Для больших двоичных объектов CSV или TSV указывает, содержат ли большие двоичные объекты заголовок.                     |
| `namePrefix`     | `string` | Если задано, указывает префикс больших двоичных объектов. При операциях записи все большие двоичные объекты будут записаны с этим префиксом. При операциях чтения считываются только большие двоичные объекты с этим префиксом. |
| `fileExtension`  | `string` | Если задано, указывает расширения файлов больших двоичных объектов. При записи имена больших двоичных объектов будут заканчиваться этим суффиксом. При чтении будут считываться только большие двоичные объекты с этим расширением файла.           |
| `encoding`       | `string` | Указывает, как текст кодируется: `UTF8NoBOM` (по умолчанию) или `UTF8BOM` .             |

Дополнительные сведения о параметрах внешней таблицы в запросах см. в разделе [логика фильтрации артефактов](#artifact-filtering-logic).

> [!NOTE]
> * Если таблица существует, команда завершится ошибкой `.create` . Используйте `.alter` для изменения существующих таблиц. 
> * Изменение схемы, формата или определения секции внешней таблицы больших двоичных объектов не поддерживается. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md) для `.create` и [разрешения администратора таблицы](../management/access-control/role-based-authorization.md) для `.alter` . 
 
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

|TableName|TableType|Папка|DocString|Свойства|ConnectionStrings|Секции|
|---|---|---|---|---|---|---|
|екстерналмултиплепартитионс|BLOB-объект|екстерналтаблес|Docs|{"Format": "CSV", "сжатый": false, "Компрессионтипе": NULL, "FileExtension": "CSV", "Инклудехеадерс": "None", "Encoding": NULL, "NamePrefix": NULL}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "CustomerName = {0} ", "ColumnName": "CustomerName", "Ordinal": 0}, PartitionBy ":" 1,00:00:00 "," ColumnName ":" timestamp "," Ordinal ": 1}]|

### <a name="artifact-filtering-logic"></a>Логика фильтрации артефактов

При запросе к внешней таблице механизм запросов повышает производительность, отфильтровывая ненужные внешние артефакты хранилища (BLOB-объекты). Ниже описан процесс выполнения итерации по BLOB-объектам и принятия решения о том, следует ли обрабатывать большой двоичный объект.

1. Создайте шаблон URI, представляющий место, где находятся большие двоичные объекты. Изначально шаблон URI равен строке подключения, предоставленной как часть определения внешней таблицы. Если определены какие бы то ни было секции, они добавляются к шаблону URI.
Например, если строка подключения имеет значение `https://storageaccount.blob.core.windows.net/container1` и определена секция даты и времени: `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)` , соответствующий шаблон URI будет выглядеть так: `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd` , и мы будем искать большие двоичные объекты в расположениях, соответствующих этому шаблону.
Если определена дополнительная строковая Секция `"CustomerId" customerId` , соответствующий шаблон URI будет следующим: `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*` .

1. Для всех *прямых* BLOB-объектов, найденных в созданных шаблонах URI, проверьте следующее:

   * Значения секций соответствуют предикатам, используемым в запросе.
   * Имя большого двоичного объекта начинается с `NamePrefix` , если такое свойство определено.
   * Имя большого двоичного объекта заканчивается на `FileExtension` , если такое свойство определено.

После соблюдения всех условий большой двоичный объект извлекается и обрабатывается обработчиком запросов.

### <a name="spark-virtual-columns-support"></a>Поддержка виртуальных столбцов Spark

При экспорте данных из Spark столбцы секционирования (заданные в методе модуля записи данных в кадре `partitionBy` ) не записываются в файлы данных. Этот процесс позволяет избежать дублирования данных, поскольку данные уже находятся в именах папок. Например, `column1=<value>/column2=<value>/` и Spark может распознать его при чтении. Однако Kusto требует, чтобы столбцы секционирования присутствовали в самих данных. Планируется поддержка виртуальных столбцов в Kusto. До этого следует использовать следующее решение: при экспорте данных из Spark создайте копию всех столбцов, на которые секционированы данные, перед записью кадра данных.

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

## <a name="show-external-table-artifacts"></a>. Отображение артефактов внешней таблицы

* Возвращает список всех артефактов, которые будут обработаны при запросе данной внешней таблицы.
* Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис** 

`.show``external` `table` *TableName*`artifacts`

**Выходные данные**

| Выходной параметр | Type   | Описание                       |
|------------------|--------|-----------------------------------|
| URI              | строка | URI внешнего артефакта хранилища |

**Примеры:**

```kusto
.show external table T artifacts
```

**Проверки**

| URI                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

## <a name="create-external-table-mapping"></a>. Создание сопоставления внешней таблицы

`.create``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* *маппингинжсонформат*

Создает новое сопоставление. Дополнительные сведения см. в разделе [сопоставления данных](./mappings.md#json-mapping).

**Пример** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "int", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"путь": "$. ROWGUID"}}] |

## <a name="alter-external-table-mapping"></a>. изменение сопоставления внешней таблицы

`.alter``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* *маппингинжсонформат*

Изменяет существующее сопоставление. 
 
**Пример** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"путь": "$. ROWGUID"}}] |

## <a name="show-external-table-mappings"></a>. Отображение сопоставлений внешних таблиц

`.show``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* Екстерналтабленаме `json``mappings`

Отображение сопоставлений (все или указанное по имени).
 
**Пример** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "ColumnType": "", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "ColumnType": "", "Properties": {"путь": "$. ROWGUID"}}] |

## <a name="drop-external-table-mapping"></a>. Удаление сопоставления внешней таблицы

`.drop``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* 

Удаляет сопоставление из базы данных.
 
**Пример** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```
