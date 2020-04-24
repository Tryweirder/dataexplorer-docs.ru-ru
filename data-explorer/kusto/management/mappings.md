---
title: Отображение данных - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны отображения данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0d94815eedfd551a09a979c57c68baf125abec40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520780"
---
# <a name="data-mappings"></a>Картографирование данных

Карты данных используются при проглатываниях для картирования входящих данных к столбкам внутри таблиц Kusto.

Kusto поддерживает различные типы `row-oriented` карт, как (CSV, JSON и AVRO), так и `column-oriented` (Паркет).

Каждый элемент в картографии построен из трех свойств:

|Свойство|Описание|
|----|--|
|`column`|Имя целевой колонки в таблице Kusto|
|`datatype`| (Необязательно) Тип данных, с помощью которого можно создать отображенная колонка, если она еще не существует в таблице Kusto|
|`Properties`|(Необязательно) Недвижимость-мешок, содержащий свойства, характерные для каждого отображения, как описано в каждом разделе ниже.


Все отображения могут быть [предварительно созданы](create-ingestion-mapping-command.md) и могут `ingestionMappingReference` быть отсылки из команды глотания с использованием параметров.

## <a name="csv-mapping"></a>Отображение CSV

Когда исходный файл является CSV (или любым форматом, разделенным на делиметр) и его схема не соответствует текущей схеме таблицы Kusto, карты ОТображения CSV от схемы файла к схеме таблицы Kusto. Если таблица не существует в Кусто, она будет создана в соответствии с этим отображением. Если некоторые поля в отображении отсутствуют в таблице, они будут добавлены. 

Отображение CSV может быть применено во всех форматах, разделенных делимитером: CSV, TSV, PSV, SCSV и SOHsv.

Каждый элемент в списке описывает отображение для определенного столбца и может содержать следующие свойства:

|Свойство|Описание|
|----|--|
|`ordinal`|Номер заказа столбца в CSV|
|`constantValue`|(Необязательно) Постоянное значение, используемое для столбца вместо некоторого значения внутри CSV|

> [!NOTE]
> `Ordinal`и `ConstantValue` являются взаимоисключающими.

### <a name="example-of-the-csv-mapping"></a>Пример отображения CSV

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
> Когда приведенное выше отображение в составе команды `.ingest` управления сериализируется как строка JSON.

* Когда приведенное выше отображение предварительно `.ingest` [создано,](create-ingestion-mapping-command.md) на ней можно ссылаться в команде управления:
```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* Когда приведенное выше отображение в составе команды `.ingest` управления сериализируется как строка JSON:

```
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

**Примечание:** Следующий формат отображения, без `Properties` имущества мешок, в настоящее время поддерживается, но может быть амортизированы в будущем.

```
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

## <a name="json-mapping"></a>JSON отображение

Когда исходный файл находится в формате JSON, содержимое файла отображается на таблице Kusto. Таблица должна существовать в базе данных Kusto, если для всех отображено столбцов не указан допустимый тип данных. Столбцы, отображаемые в отображении JSON, должны существовать в таблице Kusto, если для всех несуществующих столбцов не указан тип данных.

Каждый элемент в списке описывает отображение для определенного столбца и может содержать следующие свойства: 

|Свойство|Описание|
|----|--|
|`path`|Если начинается с `$`: JSON путь к области, которая станет содержание столбца в документе JSON (JSON путь, который обозначает весь документ ). `$` Если значение не начинается с: `$`используется постоянное значение.|
|`transform`|(Необязательно) Преобразование, которое должно быть применено к содержимому с [преобразованиями отображения.](#mapping-transformations)|

### <a name="example-of-json-mapping"></a>Пример отображения JSON

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
> Когда приведенное выше отображение в составе команды `.ingest` управления сериализируется как строка JSON.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**Примечание:** Следующий формат отображения, без `Properties` имущества мешок, в настоящее время поддерживается, но может быть амортизированы в будущем.

```
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
    
## <a name="avro-mapping"></a>Авро отображение

Когда исходный файл находится в формате Avro, содержимое файла Avro отображается в таблице Kusto. Таблица должна существовать в базе данных Kusto, если для всех отображено столбцов не указан допустимый тип данных. Столбцы, отображаемые в отображении Avro, должны существовать в таблице Kusto, если для всех несуществующих столбцов не указан тип данных.

Каждый элемент в списке описывает отображение для определенного столбца и может содержать следующие свойства: 

|Свойство|Описание|
|----|--|
|`Field`|Название поля в записи Avro.|
|`Path`|Альтернатива `field` использованию, которая позволяет взять внутреннюю часть Avro рекордного поля, если это необходимо. Значение обозначает Путь JSON от корня записи. Для получения дополнительной информации смотрите приведенные ниже примечания. |
|`transform`|(Необязательно) Преобразование, которое должно быть применено к содержимому с [поддерживаемыми преобразованиями.](#mapping-transformations)|

**Примечания**
>[!NOTE]
> * `field`и `path` не может быть использован вместе, только один допускается. 
> * `path`не может `$` указывать только на корень, он должен иметь по крайней мере один уровень пути.

Две альтернативы ниже являются равными:

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

### <a name="example-of-the-avro-mapping"></a>Пример отображения AVRO

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
> Когда приведенное выше отображение в составе команды `.ingest` управления сериализируется как строка JSON.

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**Примечание:** Следующий формат отображения, без `Properties` имущества мешок, в настоящее время поддерживается, но может быть амортизированы в будущем.

```
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

## <a name="parquet-mapping"></a>Картографирование паркета

Когда исходный файл находится в формате Parquet, содержимое файла отображается на таблице Kusto. Таблица должна существовать в базе данных Kusto, если для всех отображено столбцов не указан допустимый тип данных. Столбцы, отображаемые в картографии паркета, должны существовать в таблице Kusto, если для всех несуществующих столбцов не указан тип данных.

Каждый элемент в списке описывает отображение для определенного столбца и может содержать следующие свойства:

|Свойство|Описание|
|----|--|
|`path`|Если начинается с `$`: JSON путь к полю, который станет содержание столбца в документе `$`Паркет (JSON путь, который обозначает весь документ ). Если значение не начинается с: `$`используется постоянное значение.|
|`transform`|(Необязательно) [отображение преобразований,](#mapping-transformations) которые должны быть применены к содержимому.


### <a name="example-of-the-parquet-mapping"></a>Пример картирования паркета

``` json
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
> Когда приведенное выше отображение в составе команды `.ingest` управления сериализируется как строка JSON.

* Когда приведенное выше отображение предварительно `.ingest` [создано,](create-ingestion-mapping-command.md) на ней можно ссылаться в команде управления:

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* Когда приведенное выше отображение в составе команды `.ingest` управления сериализируется как строка JSON:

```
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

## <a name="orc-mapping"></a>Картографирование орков

Когда исходный файл находится в формате Orc, содержимое файла отображается в таблице Kusto. Таблица должна существовать в базе данных Kusto, если для всех отображено столбцов не указан допустимый тип данных. Столбцы, отображаемые в картографии Orc, должны существовать в таблице Kusto, если для всех несуществующих столбцов не указан тип данных.

Каждый элемент в списке описывает отображение для определенного столбца и может содержать следующие свойства:

|Свойство|Описание|
|----|--|
|`path`|Если начинается с `$`: JSON путь к полю, который станет содержание столбца в документе `$`Orc (JSON путь, который обозначает весь документ ). Если значение не начинается с: `$`используется постоянное значение.|
|`transform`|(Необязательно) [отображение преобразований,](#mapping-transformations) которые должны быть применены к содержимому.

### <a name="example-of-orc-mapping"></a>Пример картирования орков

``` json
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
> Когда приведенное выше отображение в составе команды `.ingest` управления сериализируется как строка JSON.

```
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

## <a name="mapping-transformations"></a>Преобразование карт

Некоторые отображения формата данных (Parquet, JSON и Avro) поддерживают простые и полезные преобразования. В тех случаях, когда сценарий требует более сложной обработки в момент проглования, используйте [политику обновления,](update-policy.md)которая позволяет определять легкую обработку с помощью выражения КЗЛ.

|Путь-зависимая трансформация|Описание|Условия|
|--|--|--|
|`PropertyBagArrayToDictionary`|Преобразует массив свойств JSON (например, «события: »n1»:«v1», «n2»:«v2»» » » » — в словарь и сериализирует его в действительный документ JSON (например, «n1»:«v1»,»n2»:«v2»»|Может быть применен `path` только при использовании|
|`GetPathElement(index)`|Извлекает элемент из заданного пути в соответствии с данным индексом (например, Путь: $.a.b.c, GetPathElement (0) q "c", GetPathElement (-1) q "b", введите строку|Может быть применен `path` только при использовании|
|`SourceLocation`|Название артефакта хранилища, который предоставил данные, введите строку (например, поле "BaseUri" капли).|
|`SourceLineNumber`|Смещение по отношению к артефакту хранилища, введите длинный (начиная с '1' и приравливания в новой записи).|
|`DateTimeFromUnixSeconds`|Преобразует число, представляющее unix-время (секунды с 1970-01-01) в строку UTC datetime|
|`DateTimeFromUnixMilliseconds`|Преобразует число, представляющее unix-время (миллисекунды с 1970-01-01) в строку UTC datetime|
|`DateTimeFromUnixMicroseconds`|Преобразует число, представляющее unix-время (микросекунды с 1970-01-01) в строку utC datetime|
|`DateTimeFromUnixNanoseconds`|Преобразует число, представляющее unix-время (наносекунды с 1970-01-01) в строку UTC datetime|