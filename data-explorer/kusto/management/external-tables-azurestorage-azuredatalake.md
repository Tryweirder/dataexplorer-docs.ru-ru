---
title: Создание и изменение внешних таблиц в службе хранилища Azure или Azure Data Lake Azure обозреватель данных
description: В этой статье описывается создание и изменение внешних таблиц в службе хранилища Azure или Azure Data Lake
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0532219b8efc1cab7508d1838882b6fa48f5048f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343272"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>Создание и изменение внешних таблиц в службе хранилища Azure или Azure Data Lake

Следующая команда описывает создание внешней таблицы, расположенной в хранилище BLOB-объектов Azure, Azure Data Lake Store Gen1 или Azure Data Lake Store Gen2. 

Общие сведения о функции внешних таблиц службы хранилища Azure см. [в статье запрос данных в Azure Data Lake с помощью обозреватель данных Azure](../../data-lake-query-data.md).

## <a name="create-or-alter-external-table"></a>. Create или. ALTER External Table

**Синтаксис**

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *[TableName](#table-name)* `(` *[Схема](#schema)* TableName`)`  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` `(` *[Секции](#partitions)* `)` [ `pathformat` `=` `(` *[пасформат](#path-format)* `)` ]]  
`dataformat``=` * [Формат](#format)*  
`(`*[StorageConnectionString](#connection-string)* [ `,` ...]`)`   
[ `with` `(` *[PropertyName](#properties)* `=` *[value](#properties)* `,` ... `)` ]  

Создает или изменяет новую внешнюю таблицу в базе данных, в которой выполняется команда.

> [!NOTE]
> * Если таблица существует, команда завершится ошибкой `.create` . Используйте `.create-or-alter` или `.alter` для изменения существующих таблиц.
> * Изменение схемы, формата или определения секции внешней таблицы больших двоичных объектов не поддерживается. 
> * Для операции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md) для `.create` и [разрешения администратора таблицы](../management/access-control/role-based-authorization.md) для `.alter` . 

**Параметры**

<a name="table-name"></a>
*TableName*

Имя внешней таблицы, которое соответствует правилам [имен сущностей](../query/schema-entities/entity-names.md) .
Имя внешней таблицы не может совпадать с именем обычной таблицы в той же базе данных.

<a name="schema"></a>
*Схемы*

Схема внешних данных описывается в следующем формате:

&nbsp;&nbsp;*ColumnName* `:` *ColumnType* [ `,` *ColumnName* `:` *ColumnType* ...]

где *ColumnName* соответствует правилам [именования сущностей](../query/schema-entities/entity-names.md) , а *ColumnType* — один из [поддерживаемых типов данных](../query/scalar-data-types/index.md).

> [!TIP]
> Если схема внешних данных неизвестна, используйте подключаемый модуль [ \_ \_ схемы хранилища](../query/inferstorageschemaplugin.md) , который помогает вывести схему на основе содержимого внешнего файла.

<a name="partitions"></a>
*Partition*

Разделенный запятыми список столбцов, по которым секционируется внешняя таблица. Столбец секционирования может существовать в самом файле данных или быть частью пути к файлу по сопоставлению безопасности (см. Дополнительные сведения в [виртуальных столбцах](#virtual-columns)).

Список секций — это любое сочетание столбцов секционирования, заданное с помощью одной из следующих форм:

* Секция, представляющая [виртуальный столбец](#virtual-columns).

  *PartitionName* `:` (`datetime` | `string`)

* Секция, основанная на значении строкового столбца.

  *PartitionName* `:` `string` `=` *ColumnName*

* Секция, основанная на [хэш-](../query/hashfunction.md)значении столбца строки, *номер*остатка от деления.

  *PartitionName* `:` `long` `=` `hash` `(` *ColumnName* `,` *Номер* ColumnName`)`

* Секция, основанная на усеченном значении столбца типа DateTime. См. документацию по функциям [startofyear](../query/startofyearfunction.md), [StartOfMonth](../query/startofmonthfunction.md), [startofweek](../query/startofweekfunction.md), [стартофдай](../query/startofdayfunction.md) или [bin](../query/binfunction.md) .

  *PartitionName* `:` `datetime` `=` ( `startofyear` \| `startofmonth` \| `startofweek` \| `startofday` ) `(` *ColumnName*`)`  
  *PartitionName* `:` `datetime` `=` `bin` `(` *ColumnName* `,` *Интервал* времени ColumnName`)`

Чтобы проверить правильность определения секционирования, используйте свойство `sampleUris` при создании внешней таблицы.

<a name="path-format"></a>
*пасформат*

Формат пути к файлу внешних URI данных, который можно указать в дополнение к секциям. Формат пути — это последовательность элементов раздела и разделителей текста:

&nbsp;&nbsp;[*Стрингсепаратор*] *Partition* [*Стрингсепаратор*] [*Секция* [*стрингсепаратор*]...]  

где *Partition* ссылается на секцию, объявленную в `partition` `by` предложении, а *стрингсепаратор* — любой текст, заключенный в кавычки. Элементы смежных секций должны быть заданы отдельно с помощью *стрингсепаратор*.

Исходный префикс пути к файлу может быть создан с помощью элементов секции, отображаемых в виде строк и разделенных соответствующими разделителями текста. Чтобы указать формат, используемый для визуализации значения секции DateTime, можно использовать следующий макрос:

&nbsp;&nbsp;`datetime_pattern``(` *DateTimeFormatое* `,` *PartitionName*`)`  

где *DateTimeFormat* соответствует спецификации формата .NET с расширением, позволяющим заключать описатели формата в фигурные скобки. Например, следующие два формата эквивалентны:

&nbsp;&nbsp;`'year='yyyy'/month='MM` перетаскивани `year={yyyy}/month={MM}`

По умолчанию значения DateTime подготавливаются к просмотру в следующих форматах:

| Функция секционирования    | Формат по умолчанию |
|-----------------------|----------------|
| `startofyear`         | `yyyy`         |
| `startofmonth`        | `yyyy/MM`      |
| `startofweek`         | `yyyy/MM/dd`   |
| `startofday`          | `yyyy/MM/dd`   |
| `bin(`*Рубрик*`, 1d)` | `yyyy/MM/dd`   |
| `bin(`*Рубрик*`, 1h)` | `yyyy/MM/dd/HH` |
| `bin(`*Рубрик*`, 1m)` | `yyyy/MM/dd/HH/mm` |

Если *пасформат* опущен из определения внешней таблицы, предполагается, что все секции в точно том же порядке, в котором они определены, разделяются с помощью `/` разделителя. Секции подготавливаются к просмотру с помощью представления строки по умолчанию.

Чтобы проверить правильность определения формата пути, используйте свойство `sampleUris` при создании внешней таблицы.

<a name="format"></a>
*Формат*

Формат данных, любой из [форматов приема](../../ingestion-supported-formats.md).

> [!NOTE]
> Использование внешней таблицы для [сценария экспорта](data-export/export-data-to-an-external-table.md) ограничено следующими форматами: `CSV` , `TSV` `JSON` и `Parquet` .

<a name="connection-string"></a>
*StorageConnectionString*

Один или несколько путей к контейнерам больших двоичных объектов хранилища BLOB-объектов Azure или Azure Data Lake Store файловых системах (виртуальные каталоги или папки), включая учетные данные.
Дополнительные сведения см. в разделе [строки подключения к хранилищу](../api/connection-strings/storage.md) .

> [!TIP]
> Предоставьте более одной учетной записи хранения, чтобы избежать регулирования хранилища при [экспорте](data-export/export-data-to-an-external-table.md) больших объемов данных во внешнюю таблицу. При экспорте будут распределяться операции записи между всеми предоставленными учетными записями. 

<a name="properties"></a>
*Необязательные свойства*

| Свойство         | Тип     | Описание       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | Папка таблицы                                                                     |
| `docString`      | `string` | Строка документирования таблицы                                                       |
| `compressed`     | `bool`   | Если задано, указывает, сжаты ли файлы как `.gz` файлы (используется только в [сценарии экспорта](data-export/export-data-to-an-external-table.md) ). |
| `includeHeaders` | `string` | Для текстовых форматов с разделителями (CSV, TSV,...) указывает, содержат ли файлы заголовок. Возможные значения: `All` (все файлы содержат заголовок), `FirstFile` (первый файл в папке содержит заголовок) `None` (файлы не содержат заголовка). |
| `namePrefix`     | `string` | Если задано, указывает префикс файлов. При операциях записи все файлы будут записаны с помощью этого префикса. При операциях чтения считываются только файлы с этим префиксом. |
| `fileExtension`  | `string` | Если задано, указывает расширения файлов. При записи имена файлов будут заканчиваться этим суффиксом. При чтении будут считываться только файлы с этим расширением.           |
| `encoding`       | `string` | Указывает, как текст кодируется: `UTF8NoBOM` (по умолчанию) или `UTF8BOM` .             |
| `sampleUris`     | `bool`   | Если этот параметр задан, в результате выполнения команды предоставляется несколько примеров URI внешних файлов данных, так как они ожидаются определением внешней таблицы (выборки возвращаются во второй таблице результатов). Этот параметр позволяет проверить, правильно ли определены *[разделы](#partitions)* и параметры *[пасформат](#path-format)* . |
| `validateNotEmpty` | `bool`   | Если задано значение, строки подключения проверяются на наличие в них содержимого. Команда завершится ошибкой, если указанное расположение URI не существует или у вас недостаточно разрешений для доступа к нему. |

> [!TIP]
> Дополнительные сведения о роли `namePrefix` и `fileExtension` свойствах, воспроизводимых в фильтрации файлов данных во время запроса, см. в разделе [логика фильтрации файлов](#file-filtering) .
 
<a name="examples"></a>
**Примеров** 

Внешняя таблица, не относящаяся к секционированию. Предполагается, что файлы данных помещаются непосредственно под определенными контейнерами:

```kusto
.create external table ExternalTable (x:long, s:string)  
kind=blob 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

Внешняя таблица, секционированная по датам. Предполагается, что файлы даты помещаются в каталоги по умолчанию в формате DateTime `yyyy/MM/dd` :

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by (Date:datetime = bin(Timestamp, 1d)) 
dataformat=csv 
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
```

Внешняя таблица, секционированная по месяцам, с форматом каталога `year=yyyy/month=MM` :

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=blob 
partition by (Month:datetime = startofmonth(Timestamp)) 
pathformat = (datetime_pattern("'year='yyyy'/month='MM", Month)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

Внешняя таблица, секционированная сначала по имени клиента, затем по дате. Ожидаемая структура каталогов —, например `customer_name=Softworks/2019/02/01` :

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerNamePart:string = CustomerName, Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_name=" CustomerNamePart "/" Date)
dataformat=csv 
(  
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
)
```

Внешняя таблица, секционированная сначала по хэшу имени клиента (по модулю 10), затем по дате. Ожидаемая структура каталогов —, например `customer_id=5/dt=20190201` . Имена файлов данных оканчиваются на `.txt` расширение:

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerId:long = hash(CustomerName, 10), Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_id=" CustomerId "/dt=" datetime_pattern("yyyyMMdd", Date)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with (fileExtension = ".txt")
```

**Образец вывода**

|TableName|TableType|Папка|DocString|Свойства|ConnectionStrings|Секции|пасформат|
|---------|---------|------|---------|----------|-----------------|----------|----------|
|екстерналтабле|BLOB-объект|екстерналтаблес|Docs|{"Format": "CSV", "сжатый": false, "Компрессионтипе": NULL, "FileExtension": NULL, "Инклудехеадерс": "None", "Encoding": NULL, "NamePrefix": NULL}|["https://storageaccount.blob.core.windows.net/container1;\*\*\*\*\*\*\*"]|[{"Mod": 10, "Name": "CustomerId", "ColumnName": "CustomerName", "Ordinal": 0}, {"функция": "Стартофдай", "Name": "Date", "имя_столбца": "timestamp", "Ordinal": 1}]|"Customer \_ ID =" CustomerID "/DT =" шаблон даты и времени \_ ("ГГГГММДД", Date)|

<a name="virtual-columns"></a>
**Виртуальные столбцы**

При экспорте данных из Spark столбцы секционирования (заданные в методе модуля записи данных в кадре `partitionBy` ) не записываются в файлы данных. Этот процесс позволяет избежать дублирования данных, поскольку данные уже находятся в именах папок. Например, `column1=<value>/column2=<value>/` и Spark может распознать его при чтении.

Внешние таблицы поддерживают следующий синтаксис для указания виртуальных столбцов:

```kusto
.create external table ExternalTable (EventName:string, Revenue:double)  
kind=blob  
partition by (CustomerName:string, Date:datetime)  
pathformat = ("customer=" CustomerName "/date=" datetime_pattern("yyyyMMdd", Date))  
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

<a name="file-filtering"></a>
**Логика фильтрации файлов**

При запросе к внешней таблице механизм запросов повышает производительность, отфильтровывая ненужные файлы внешнего хранилища. Ниже описан процесс выполнения итерации по файлам и принятия решения о необходимости обработки файла.

1. Создайте шаблон URI, представляющий место, где находятся файлы. Изначально шаблон URI равен строке подключения, предоставленной как часть определения внешней таблицы. Если определенные секции определены, они подготавливаются к просмотру с помощью *[пасформат](#path-format)*, а затем добавляются к шаблону URI.

2. Для всех файлов, найденных в созданных шаблонах URI, проверьте следующее:

   * Значения секций соответствуют предикатам, используемым в запросе.
   * Имя большого двоичного объекта начинается с `NamePrefix` , если такое свойство определено.
   * Имя большого двоичного объекта заканчивается на `FileExtension` , если такое свойство определено.

После выполнения всех условий файл извлекается и обрабатывается обработчиком запросов.

> [!NOTE]
> Исходный шаблон URI создается с помощью значений предиката запроса. Это лучше подходит для ограниченного набора строковых значений, а также для закрытых диапазонов времени.

## <a name="show-external-table-artifacts"></a>. Отображение артефактов внешней таблицы

Возвращает список всех файлов, которые будут обрабатываться при запросе данной внешней таблицы.

> [!NOTE]
> Для операции требуются [разрешения пользователя базы данных](../management/access-control/role-based-authorization.md).

**Синтаксис** 

`.show``external` `table` *TableName* `artifacts` [ `limit` *MaxResults*]

где *MaxResults* — необязательный параметр, который можно задать для ограничения количества результатов.

**Выходные данные**

| Выходной параметр | Тип   | Описание                       |
|------------------|--------|-----------------------------------|
| URI              | строка | URI внешнего файла данных хранилища |
| Размер             | long   | Длина файла в байтах              |
| Секция        | Динамический | Динамический объект, описывающий файловые секции для секционированной внешней таблицы |

> [!TIP]
> Итерация по всем файлам, на которые ссылается внешняя таблица, может быть довольно дорогостоящей в зависимости от числа файлов. Обязательно используйте `limit` параметр, если вы просто хотите увидеть некоторые примеры URI.

**Примеры:**

```kusto
.show external table T artifacts
```

**Выходные данные:**

| URI                                                                     | Размер | Секция |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` | 10743 | `{}`   |


Для секционированной таблицы `Partition` столбец будет содержать извлеченные значения секции:

**Выходные данные:**

| URI                                                                     | Размер | Секция |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/customer=john.doe/dt=20200101/file.csv` | 10743 | `{"Customer": "john.doe", "Date": "2020-01-01T00:00:00.0000000Z"}` |


## <a name="create-external-table-mapping"></a>. Создание сопоставления внешней таблицы

`.create``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* *маппингинжсонформат*

Создает новое сопоставление. Дополнительные сведения см. в разделе [сопоставления данных](./mappings.md#json-mapping).

**Пример** 
 
```kusto
.create external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "Properties": {"путь": "$. ROWGUID"}}] |

## <a name="alter-external-table-mapping"></a>. изменение сопоставления внешней таблицы

`.alter``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* *маппингинжсонформат*

Изменяет существующее сопоставление. 
 
**Пример** 
 
```kusto
.alter external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "Properties": {"путь": "$. ROWGUID"}}] |

## <a name="show-external-table-mappings"></a>. Отображение сопоставлений внешних таблиц

`.show``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* Екстерналтабленаме `json``mappings`

Отображение сопоставлений (все или указанное по имени).
 
**Пример** 
 
```kusto
.show external table MyExternalTable json mapping "Mapping1" 

.show external table MyExternalTable json mappings 
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "RowNumber", "Properties": {"путь": "$. RowNumber"}}, {"ColumnName": "ROWGUID", "Properties": {"путь": "$. ROWGUID"}}] |

## <a name="drop-external-table-mapping"></a>. Удаление сопоставления внешней таблицы

`.drop``external` `table` *Екстерналтабленаме* `json` `mapping` *MappingName* 

Удаляет сопоставление из базы данных.
 
**Пример** 
 
```kusto
.drop external table MyExternalTable json mapping "Mapping1" 
```
## <a name="next-steps"></a>Дальнейшие шаги

* [Команды для общего управления внешней таблицей](./external-table-commands.md)
* [Создание и изменение внешних таблиц SQL](external-sql-tables.md)