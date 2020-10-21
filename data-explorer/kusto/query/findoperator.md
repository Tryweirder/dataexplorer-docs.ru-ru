---
title: Поиск оператора в Azure обозреватель данных
description: В этой статье описывается оператор Find в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d1e01f366c1bae677111c67b0e60fde59683706e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245051"
---
# <a name="find-operator"></a>Оператор find

Находит строки, соответствующие предикату в наборе таблиц.

::: zone pivot="azuredataexplorer"

Область действия `find` также может быть между несколькими базами данных или между кластерами.

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"

find in (database('*').*) where Fruit == "apple"

find in (cluster('cluster_name').database('MyDB*'.*)) where Fruit == "apple"
```

::: zone-end

::: zone pivot="azuremonitor"

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"
```

::: zone-end

## <a name="syntax"></a>Синтаксис

* `find`[ `withsource` = *ColumnName*] [ `in` `(` *Таблица* [ `,` *Таблица*,...] `)` ] `where` *Предикат* [ `project-smart`  |  `project` *ColumnName* [ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`,` `pack(*)`]] 

* `find`*Предикат* [ `project-smart`  |  `project` *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`, pack(*)`]] 

## <a name="arguments"></a>Аргументы

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: необязательный. По умолчанию выходные данные будут включать столбец с именем *source_* , значения которого указывают, какая исходная таблица участвовала в каждой строке. Если указано, вместо *source_* будет использоваться *ColumnName* .
Если запрос, сопоставленный с подстановочными знаками, ссылается на таблицы из нескольких баз данных (включая базу данных по умолчанию), то значение этого столбца будет иметь имя таблицы, дополненное базой данных. Аналогичные значения для *кластеров* и *баз данных* будут присутствовать в значении, если имеется ссылка на несколько кластеров.
* *Predicate*: `boolean` [выражение](./scalar-data-types/bool.md) для столбцов *таблицы* входных таблиц [ `,` *Таблица*,...]. Он вычисляется для каждой строки в каждой входной таблице. Дополнительные сведения см. в разделе  [сведения о синтаксисе предиката](./findoperator.md#predicate-syntax).
* `Table`: Необязательный параметр. По умолчанию *Поиск* будет искать во всех таблицах текущей базы данных:
    *  Имя таблицы, например `Events`
    *  Выражение запроса, например `(Events | where id==42)`
    *  Набор таблиц, указанных с помощью подстановочного знака. Например, `E*` формирует объединение всех таблиц в базе данных, имена которых начинаются с `E` .
* `project-smart` | `project`: Если не указано, `project-smart` будет использоваться по умолчанию. Дополнительные сведения см. в разделе [сведения о схеме вывода](./findoperator.md#output-schema).

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: необязательный. По умолчанию выходные данные содержат столбец с именем *source_* , значения которого указывают, какая исходная таблица участвует в каждой строке. Если указано, вместо *source_* будет использоваться *ColumnName* .
* *Predicate*: `boolean` [выражение](./scalar-data-types/bool.md) для столбцов *таблицы* входных таблиц [ `,` *Таблица*,...]. Он вычисляется для каждой строки в каждой входной таблице. Дополнительные сведения см. в разделе  [сведения о синтаксисе предиката](./findoperator.md#predicate-syntax).
* `Table`: Необязательный параметр. По *умолчанию поиск будет* выполнять поиск по всем таблицам:
    *  Имя таблицы, например `Events` 
    *  Выражение запроса, например `(Events | where id==42)`
    *  Набор таблиц, указанных с помощью подстановочного знака. Например, `E*` формирует объединение всех таблиц, имена которых начинаются с `E` .
* `project-smart` | `project`: Если не указано `project-smart` , будет использоваться по умолчанию. Дополнительные сведения см. в разделе [сведения о схеме вывода](./findoperator.md#output-schema).

::: zone-end

## <a name="returns"></a>Результаты

Преобразование строк в *таблице* [ `,` *Таблица*,...], для которых *предикат* имеет `true` . Строки преобразуются в соответствии со [схемой вывода](#output-schema).

## <a name="output-schema"></a>Схема вывода

**source_ столбец**

В выходных данных оператора Find всегда будет содержаться *source_* столбец с именем исходной таблицы. Столбец можно переименовать с помощью `withsource` параметра.

**столбцы результатов**

Исходные таблицы, которые не содержат столбцов, используемых при вычислении предиката, будут отфильтрованы.

При использовании `project-smart` столбцы, которые будут отображаться в выходных данных, будут выглядеть следующим образом:
* Столбцы, которые отображаются явно в предикате.
* Столбцы, общие для всех отфильтрованных таблиц.

Остальные столбцы будут упакованы в контейнер свойств и будут отображаться в дополнительном `pack_` столбце.
Столбец, на который явно ссылается предикат и появляется в нескольких таблицах с несколькими типами, будет иметь отдельный столбец в схеме результатов для каждого такого типа. Каждое из имен столбцов будет построено на основе имени и типа исходного столбца, разделенных символом подчеркивания.

При использовании `project` *ColumnName*[ `:` *ColumnType*] [ `,` *ColumnName*[ `:` *ColumnType*],...] [`,` `pack(*)`]:
* В таблице результатов будут содержаться столбцы, указанные в списке. Если исходная таблица не содержит определенного столбца, то значения в соответствующих строках будут иметь значение null.
* При указании *ColumnType* с *ColumnName*этот столбец в "Result" будет иметь заданный тип, и при необходимости значения будут приведены к этому типу. Приведение не оказывает влияния на тип столбца при вычислении *предиката*.
* Если `pack(*)` используется, остальные столбцы будут упакованы в контейнер свойств и будут отображаться в дополнительном `pack_` столбце.

**pack_ столбец**

Этот столбец будет содержать контейнер свойств с данными из всех столбцов, которые не отображаются в схеме вывода. Имя исходного столбца будет служить именем свойства, а значение столбца будет служить значением свойства.

## <a name="predicate-syntax"></a>Синтаксис предиката

Оператор *Find* поддерживает альтернативный синтаксис для `* has` термина и, используя только *условия*, будет искать термин во всех входных столбцах.

Общие сведения о некоторых функциях фильтрации см. в разделе [оператор WHERE](./whereoperator.md).

## <a name="notes"></a>Примечания

* Если `project` предложение ссылается на столбец, который встречается в нескольких таблицах и имеет несколько типов, то тип должен следовать этой ссылке на столбец в предложении Project.
* Если столбец отображается в нескольких таблицах и имеет несколько типов и используется `project-smart` , то для каждого типа в результате будет указан соответствующий столбец `find` , как описано в разделе [Union](./unionoperator.md) .
* При использовании *смарт-проекта*, изменения в предикате, в наборе исходных таблиц или в схеме таблиц могут привести к изменению выходной схемы. Если требуется схема с постоянным результатом, используйте вместо нее *проект*
* `find` область не может включать [функции](../management/functions.md). Чтобы включить функцию в область поиска, определите [инструкцию Let](./letstatement.md) с [ключевым словом View](./letstatement.md).

## <a name="performance-tips"></a>Советы по улучшению производительности

* Используйте [таблицы](../management/tables.md) в отличие от [табличных выражений](./tabularexpressionstatements.md).
Если табличное выражение, оператор Find возвращается к `union` запросу, что может привести к снижению производительности.
* Если столбец, отображающийся в нескольких таблицах и имеющий несколько типов, является частью предложения Project, предпочтительно добавить *ColumnType* в предложение Project, чтобы изменить таблицу перед ее передачей `find` .
* Добавьте в предикат фильтры на основе времени. Используйте значение столбца DateTime или [ingestion_time ()](./ingestiontimefunction.md).
* Поиск в конкретных столбцах, а не в полнотекстовом поиске.
* Лучше не ссылаться на столбцы, которые появляются в нескольких таблицах и имеют несколько типов. Если предикат является допустимым при разрешении таких столбцов для нескольких типов, запрос будет возвращаться к объединению.
Например, см. [примеры случаев, когда Find будет действовать как объединение](./findoperator.md#examples-of-cases-where-find-will-act-as-union).
 
## <a name="examples"></a>Примеры

::: zone pivot="azuredataexplorer"

### <a name="term-lookup-across-all-tables-in-current-database"></a>Поиск терминов во всех таблицах в текущей базе данных

Запрос находит все строки из всех таблиц в текущей базе данных, в которых любой столбец содержит слово `Kusto` .
Результирующие записи преобразуются в соответствии со [схемой вывода](#output-schema).

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>Поиск терминов во всех таблицах, соответствующих шаблону имени в текущей базе данных

Запрос находит все строки из всех таблиц в текущей базе данных, имя которых начинается с `K` , а в любом столбце содержится слово `Kusto` .
Результирующие записи преобразуются в соответствии со [схемой вывода](#output-schema).

```kusto
find in (K*) where * has "Kusto"
```

### <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>Поиск терминов по всем таблицам во всех базах данных в кластере

Запрос находит все строки из всех таблиц во всех базах данных, в которых в любом столбце содержится слово `Kusto` .
Этот запрос является запросом [между базами данных](./cross-cluster-or-database-queries.md) .
Результирующие записи преобразуются в соответствии со [схемой вывода](#output-schema).

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>Поиск терминов во всех таблицах и базах данных, соответствующих шаблону имени в кластере

Запрос находит все строки из всех таблиц, имя которых начинается со `K` слова во всех базах данных, имя которых начинается с `B` и где в любом столбце содержится слово `Kusto` .
Результирующие записи преобразуются в соответствии со [схемой вывода](#output-schema).

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>Поиск терминов в нескольких кластерах

Запрос находит все строки из всех таблиц, имя которых начинается со `K` слова во всех базах данных, имя которых начинается с `B` и где в любом столбце содержится слово `Kusto` .
Результирующие записи преобразуются в соответствии со [схемой вывода](#output-schema).

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>Поиск терминов во всех таблицах

Запрос находит все строки из всех таблиц, в которых любой столбец содержит слово `Kusto` .
Результирующие записи преобразуются в соответствии со [схемой вывода](#output-schema).

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>Примеры `find` выходных результатов  

В следующих примерах показано, как `find` можно использовать две таблицы: *EventsTable1* и *EventsTable2*.
Предположим, что у нас есть следующее содержимое этих двух таблиц:

### <a name="eventstable1"></a>EventsTable1

|Session_Id|Level|евенттекст|Версия
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Некоторые Текст1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Некоторые Текст2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Error|Некоторые Text3|v 1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|Сведения|Некоторые Text4|v 1.1.0

### <a name="eventstable2"></a>EventsTable2

|Session_Id|Level|евенттекст|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Сведения|Другой Текст1|Event1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Другой Текст2|Event2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Другие Text3|Event3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|Error|Другие Text4|Event4

### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>Поиск в общих столбцах, общих и нестандартных столбцах, а также упаковка остальных  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|евенттекст|Версия|EventName|pack_
|---|---|---|---|---|
|EventsTable1|Некоторые Текст2|v1.0.0||{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error"}
|EventsTable2|Другие Text3||Event3|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error"}


### <a name="search-in-common-and-uncommon-columns"></a>Поиск в общих и нестандартных столбцах

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|евенттекст|Версия|EventName|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Некоторые Текст1|v1.0.0
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Некоторые Текст2|v1.0.0
|EventsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Другой Текст1||Event1

Примечание. на практике *EventsTable1* строки будут отфильтрованы с помощью ```Version == 'v1.0.0'``` предиката, а строки *EventsTable2* будут отфильтрованы с помощью ```EventName == 'Event1'``` предиката.

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>Использовать сокращенную нотацию для поиска по всем таблицам в текущей базе данных

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Level|евенттекст|pack_|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Некоторые Текст1|{"Версия": "v 1.0.0"}
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Некоторые Текст2|{"Версия": "v 1.0.0"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Другой Текст2|{"EventName": "Event2"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Другие Text3|{"EventName": "Event3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>Возврат результатов из каждой строки в качестве контейнера свойств

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "сведения", "Евенттекст": "some текст1", "Version": "v 1.0.0"}
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error", "Евенттекст": "some Текст2", "Version": "v 1.0.0"}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "сведения", "Евенттекст": "другой Текст2", "EventName": "Event2"}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error", "Евенттекст": "другие Text3", "EventName": "Event3"}


## <a name="examples-of-cases-where-find-will-act-as-union"></a>Примеры случаев, когда `find` будут действовать как `union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>Использование не табличного выражения в качестве операнда Find

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>Ссылка на столбец, который появляется в нескольких таблицах и имеет несколько типов

Предположим, что мы создали две таблицы, выполнив: 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* Следующий запрос будет выполнен как `union` .

```kusto
find in (Table1, Table2) where ProcessId == 1001
```

Выходная схема результата будет иметь значение *(уровень: строка, метка времени, ProcessId_string, ProcessId_int)*.

* Следующий запрос также будет выполнен как `union` , но будет создавать другую схему результатов.

```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

Выходная схема результата будет иметь значение *(уровень: строка, метка времени, ProcessId_string)*
