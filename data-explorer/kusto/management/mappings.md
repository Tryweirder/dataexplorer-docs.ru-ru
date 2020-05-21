---
title: Сопоставления данных в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются сопоставления данных в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: ohbitton
ms.service: data-explorer
ms.topic: reference
ms.date: 05/19/2020
ms.openlocfilehash: c4a64db6d1103aa2a004b816969ab73c7ba19943
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722071"
---
# <a name="data-mappings"></a>Сопоставления данных

Сопоставления данных используются во время приема для сопоставления входящих данных со столбцами внутри Kusto таблиц.

Kusto поддерживает различные типы сопоставлений `row-oriented` (CSV, JSON и Avro) и `column-oriented` (Parquet).

Каждый элемент в списке сопоставлений состоит из трех свойств:

|Свойство|Описание|
|----|--|
|`column`|Имя целевого столбца в таблице Kusto|
|`datatype`| Используемых DataType, с помощью которого создается сопоставленный столбец, если он еще не существует в таблице Kusto|
|`Properties`|Используемых Контейнер свойств, содержащий свойства, относящиеся к каждому сопоставлению, как описано в каждом разделе ниже.


Все сопоставления можно [создавать заранее](create-ingestion-mapping-command.md) , и на них можно ссылаться из команды приема с помощью `ingestionMappingReference` параметров.

## <a name="csv-mapping"></a>Сопоставление CSV

Если исходный файл является CSV-файлом (или любым разделителем форматом) и его схема не совпадает с текущей схемой таблицы Kusto, сопоставление CSV сопоставляет схему файла со схемой таблицы Kusto. Если таблица не существует в Kusto, она будет создана в соответствии с этим сопоставлением. Если в таблице отсутствуют некоторые поля в сопоставлении, они будут добавлены. 

Сопоставление CSV может применяться ко всем форматам с разделителями-разделителями: CSV, TSV, ПСВ, СКСВ и Сохсв.

Каждый элемент в списке описывает сопоставление для определенного столбца и может содержать следующие свойства:

|Свойство|Описание|
|----|--|
|`ordinal`|Порядковый номер столбца в CSV-файле|
|`constantValue`|Используемых Постоянное значение, используемое для столбца, а не какое-либо значение в CSV-файле|

> [!NOTE]
> `Ordinal`и `ConstantValue` являются взаимоисключающими.

### <a name="example-of-the-csv-mapping"></a>Пример сопоставления CSV

``` json
[
  { "column" : "rownumber", "Properties":{"Ordinal":"0"}},
  { "column" : "rowguid",   "Properties":{"Ordinal":"1"}},
  { "column" : "xdouble",   "Properties":{"Ordinal":"2"}},
  { "column" : "xbool",     "Properties":{"Ordinal":"3"}},
  { "column" : "xint32",    "Properties":{"Ordinal":"4"}},
  { "column" : "xint64",    "Properties":{"Ordinal":"5"}},
  { "column" : "xdate",     "Properties":{"Ordinal":"6"}},
  { "column" : "xtext",     "Properties":{"Ordinal":"7"}},
  { "column" : "const_val", "Properties":{"ConstValue":"Sample: constant value"}}
]
```

> [!NOTE]
> Если приведенное выше сопоставление предоставляется как часть `.ingest` управляющей команды, оно сериализуется как строка JSON.

* Если приведенное выше сопоставление является [предварительно созданным](create-ingestion-mapping-command.md) , на него можно ссылаться с помощью `.ingest` команды Control:

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* Если приведенное выше сопоставление предоставляется как часть `.ingest` управляющей команды, оно сериализуется как строка JSON:

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Properties\":{\"Ordinal\":0}},"
            "{\"column\":\"rowguid\",  \"Properties\":{\"Ordinal\":1}}"
        "]" 
    )
```

**Примечание.** Не рекомендуется использовать следующий формат сопоставления без `Properties` контейнера свойств.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Ordinal\": 0},"
            "{\"column\":\"rowguid\",  \"Ordinal\": 1}"
        "]" 
    )
```

## <a name="json-mapping"></a>Сопоставление JSON

Если исходный файл имеет формат JSON, содержимое файла сопоставляется с таблицей Kusto. Таблица должна существовать в базе данных Kusto, если для всех сопоставленных столбцов не задан допустимый тип данных. Столбцы, сопоставленные в сопоставлении JSON, должны существовать в таблице Kusto, если только не задан тип данных для всех несуществующих столбцов.

Каждый элемент в списке описывает сопоставление для определенного столбца и может содержать следующие свойства: 

|Свойство|Описание|
|----|--|
|`path`|Если начинается с `$` : JSON путь к полю, которое станет содержимым столбца в документе JSON (путь JSON, обозначающий весь документ `$` ). Если значение не начинается с `$` : используется постоянное значение.|
|`transform`|Используемых Преобразование, которое должно применяться к содержимому с [преобразованиями сопоставления](#mapping-transformations).|

### <a name="example-of-json-mapping"></a>Пример сопоставления JSON

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "rowguid",     "Properties":{"Path":"$.rowguid"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint32",      "Properties":{"Path":"$.xint32"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```

> [!NOTE]
> Если приведенное выше сопоставление предоставляется как часть `.ingest` управляющей команды, оно сериализуется как строка JSON.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**Примечание.** Не рекомендуется использовать следующий формат сопоставления без `Properties` контейнера свойств.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "json", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"path\":\"$.rownumber\"},"
        "{\"column\":\"rowguid\",  \"path\":\"$.rowguid\"}"
      "]"
  )
```
    
## <a name="avro-mapping"></a>Сопоставление Avro

Если исходный файл имеет формат Avro, содержимое файла Avro сопоставляется с таблицей Kusto. Таблица должна существовать в базе данных Kusto, если для всех сопоставленных столбцов не задан допустимый тип данных. Столбцы, сопоставленные в сопоставлении Avro, должны существовать в таблице Kusto, если только не задан тип данных для всех несуществующих столбцов.

Каждый элемент в списке описывает сопоставление для определенного столбца и может содержать следующие свойства: 

|Свойство|Описание|
|----|--|
|`Field`|Имя поля в записи Avro.|
|`Path`|Альтернатива использованию, `field` которая позволяет при необходимости использовать внутреннюю часть поля записи Avro. Значение обозначает JSON-путь из корня записи. Дополнительные сведения см. в примечаниях ниже. |
|`transform`|Используемых Преобразование, которое должно применяться к содержимому с [поддерживаемыми преобразованиями](#mapping-transformations).|

**Примечания**
>[!NOTE]
> * `field`и `path` не могут использоваться вместе, допускается только один. 
> * `path`не может указывать на `$` только корень, он должен иметь по крайней мере один уровень пути.

Два варианта ниже равны.

``` json
[
  {"column": "rownumber", "Properties":{"Path":"$.RowNumber"}}
]
```

``` json
[
  {"column": "rownumber", "Properties":{"Field":"RowNumber"}}
]
```

### <a name="example-of-the-avro-mapping"></a>Пример сопоставления AVRO

``` json
[
  {"column": "rownumber", "Properties":{"Field":"rownumber"}},
  {"column": "rowguid",   "Properties":{"Field":"rowguid"}},
  {"column": "xdouble",   "Properties":{"Field":"xdouble"}},
  {"column": "xboolean",  "Properties":{"Field":"xboolean"}},
  {"column": "xint32",    "Properties":{"Field":"xint32"}},
  {"column": "xint64",    "Properties":{"Field":"xint64"}},
  {"column": "xdate",     "Properties":{"Field":"xdate"}},
  {"column": "xtext",     "Properties":{"Field":"xtext"}}
]
``` 

> [!NOTE]
> Если приведенное выше сопоставление предоставляется как часть `.ingest` управляющей команды, оно сериализуется как строка JSON.

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**Примечание.** Не рекомендуется использовать следующий формат сопоставления без `Properties` контейнера свойств.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "avro", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"field\":\"rownumber\"},"
        "{\"column\":\"rowguid\",  \"field\":\"rowguid\"}"
      "]"
  )
```

## <a name="parquet-mapping"></a>Сопоставление Parquet

Если исходный файл имеет формат Parquet, содержимое файла сопоставляется с таблицей Kusto. Таблица должна существовать в базе данных Kusto, если для всех сопоставленных столбцов не задан допустимый тип данных. Столбцы, сопоставленные в сопоставлении Parquet, должны существовать в таблице Kusto, если только не задан тип данных для всех несуществующих столбцов.

Каждый элемент в списке описывает сопоставление для определенного столбца и может содержать следующие свойства:

|Свойство|Описание|
|----|--|
|`path`|Если начинается с `$` : JSON путь к полю, которое станет содержимым столбца в документе Parquet (путь JSON, обозначающий весь документ `$` ). Если значение не начинается с `$` : используется постоянное значение.|
|`transform`|Используемых [сопоставление преобразований](#mapping-transformations) , которые должны применяться к содержимому.


### <a name="example-of-the-parquet-mapping"></a>Пример сопоставления Parquet

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> Если приведенное выше сопоставление предоставляется как часть `.ingest` управляющей команды, оно сериализуется как строка JSON.

* Если приведенное выше сопоставление является [предварительно созданным](create-ingestion-mapping-command.md) , на него можно ссылаться с помощью `.ingest` команды Control:

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* Если приведенное выше сопоставление предоставляется как часть `.ingest` управляющей команды, оно сериализуется как строка JSON:

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "parquet", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="orc-mapping"></a>Сопоставление ORC

Если исходный файл имеет формат ORC, содержимое файла сопоставляется с таблицей Kusto. Таблица должна существовать в базе данных Kusto, если для всех сопоставленных столбцов не задан допустимый тип данных. Столбцы, сопоставленные в сопоставлении ORC, должны существовать в таблице Kusto, если только не задан тип данных для всех несуществующих столбцов.

Каждый элемент в списке описывает сопоставление для определенного столбца и может содержать следующие свойства:

|Свойство|Описание|
|----|--|
|`path`|Если начинается с `$` : JSON путь к полю, которое станет содержимым столбца в документе ORC (путь JSON, обозначающий весь документ `$` ). Если значение не начинается с `$` : используется постоянное значение.|
|`transform`|Используемых [сопоставление преобразований](#mapping-transformations) , которые должны применяться к содержимому.

### <a name="example-of-orc-mapping"></a>Пример сопоставления ORC

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> Если приведенное выше сопоставление предоставляется как часть `.ingest` управляющей команды, оно сериализуется как строка JSON.

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "orc", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="mapping-transformations"></a>Преобразования сопоставления

Некоторые сопоставления форматов данных (Parquet, JSON и Avro) поддерживают простые и полезные преобразования во время приема. Когда сценарий требует более сложной обработки во время приема, используйте [политику обновления](update-policy.md), которая позволяет определить упрощенную обработку с помощью выражения ККЛ.

|Преобразование, зависящее от пути|Описание|Условия|
|--|--|--|
|`PropertyBagArrayToDictionary`|Преобразует массив JSON свойств (например, {Events: [{"N1": "v1"}, {"N2": "v2"}]}) в словарь и сериализует его в допустимый документ JSON (например, {"N1": "v1", "N2": "v2"}).|Может применяться, только если `path` используется|
|`GetPathElement(index)`|Извлекает элемент из заданного пути в соответствии с заданным индексом (например, путь: $. a. b. c, Жетпаселемент (0) = = "c", Жетпаселемент (-1) = = "b", строка типа|Может применяться, только если `path` используется|
|`SourceLocation`|Имя артефакта хранилища, который предоставил данные, введите строку (например, поле "BaseUri" большого двоичного объекта).|
|`SourceLineNumber`|Смещение относительно этого артефакта хранилища, введите Long (начиная с "1" и увеличивая его на каждую новую запись).|
|`DateTimeFromUnixSeconds`|Преобразует число, представляющее UNIX-time (с, начиная с 1970-01-01), в строку времени в формате UTC|
|`DateTimeFromUnixMilliseconds`|Преобразует число, представляющее UNIX-время (в миллисекундах с 1970-01-01) в строку времени в формате UTC|
|`DateTimeFromUnixMicroseconds`|Преобразует число, представляющее UNIX-время (в микросекундах с 1970-01-01), в строку времени в формате UTC|
|`DateTimeFromUnixNanoseconds`|Преобразует число, представляющее UNIX-время (в наносекундах с 1970-01-01), в строку времени в формате UTC|
