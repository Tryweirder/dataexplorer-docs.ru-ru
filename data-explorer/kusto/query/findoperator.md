---
title: найти оператора - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор поиска в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4c3db23128c47c86639f15286cbcbcb748157386
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765922"
---
# <a name="find-operator"></a>Оператор find

Находит строки, которые соответствуют предикату в наборе таблиц.

::: zone pivot="azuredataexplorer"

Область действия `find` также может быть кросс-базой данных или кросс-кластером.

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

* `find``withsource`=*КолонкаИмя* `in` `(` *Таблица* `,` - *Таблица*, ...» `)` `where` *Предикатите* :`project-smart`  |  `project` *КолонкаИмя* -`:`*ColumnType*`,` - *колонкаНазвание*- ColumnName ,`:`*columnType*, ..." [`,` `pack(*)`]] 

* `find`*Предикатите* `project-smart`  |  `project` :`:` *КолонкаИмя*-`:`*ColumnType*`,` - *колонкаНазвание*- ColumnType ,*колонкаType*, ..." [`, pack(*)`]] 

## <a name="arguments"></a>Аргументы

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: Необязательно. По умолчанию выход будет включать столбец под названием *source_* значения которого указывают, какая таблица исходного кода внесла каждый вклад в каждую строку. Если указано, *columnName* будет использоваться вместо *source_* .
Если запрос эффективно (после сопоставления подстановочных карт) ссылается на таблицы из нескольких баз данных (база данных по умолчанию всегда учитывается), значение этого столбца будет иметь имя таблицы, квалифицируемое с базой данных. Аналогичным образом квалификации __кластера и базы данных__ будут присутствовать в значении, если на них будет ссылаться более одного кластера.
* *Предикат*: [см.](./findoperator.md#predicate-syntax) `boolean` [Выражение](./scalar-data-types/bool.md) над столбцов входных таблиц *Таблица* -`,` *Таблица*, ...». Он оценивается для каждой строки в каждой таблице ввода. 
* `Table`: необязательный параметр. По умолчанию *поиск* будет искать во всех таблицах текущей базы данных
    *  Название таблицы, например `Events` или
    *  Выражение запроса, например `(Events | where id==42)`
    *  Набор таблиц, указанных с помощью подстановочного знака. Например, `E*` сформируется объединение всех таблиц в базе `E`данных, имена которых начинаются.
* `project-smart` | `project`: [см. детали,](./findoperator.md#output-schema) если не указаны `project-smart` будут использованы по умолчанию

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: Необязательно. По умолчанию выход будет включать столбец под названием *source_* значения которого указывают, какая таблица исходного кода внесла каждый вклад в каждую строку. Если указано, *columnName* будет использоваться вместо *source_* .
* *Предикат*: [см.](./findoperator.md#predicate-syntax) `boolean` [Выражение](./scalar-data-types/bool.md) над столбцов входных таблиц *Таблица* -`,` *Таблица*, ...». Он оценивается для каждой строки в каждой таблице ввода. 
* `Table`: необязательный параметр. По умолчанию *находка* будет искать во всех таблицах
    *  Название таблицы, например `Events` или
    *  Выражение запроса, например `(Events | where id==42)`
    *  Набор таблиц, указанных с помощью подстановочного знака. Например, `E*` будет формировать сядевое всех `E`таблиц, имена которых начинаются .
* `project-smart` | `project`: [см. детали,](./findoperator.md#output-schema) если не указаны `project-smart` будут использованы по умолчанию

::: zone-end

## <a name="returns"></a>Результаты

Преобразование строк *Table* в`,` таблице » *Таблица*, `true`...», для которых *Predicate* является . Строки преобразуются в соответствии с схемой вывода, описанной ниже.

## <a name="output-schema"></a>Схема вывода

**source_ столбец**

Выход оператора поиска всегда будет включать *source_* столбец с именем исходной таблицы. Столбец может быть переименован `withsource` с помощью параметра.

**столбцы результатов**

Исходные таблицы, не содержащие столбца, используемого в оценке предикатов, будут отфильтрованы.

При `project-smart`использовании столбцов, которые будут отображаться в выходе, будут:
1. Столбцы, явно отображаемые в предикате
2. Столбцы, общие для всех отфильтрованных таблиц Остальные столбцы, будут упакованы в `pack_` пакет свойств и будут отображаться в дополнительном столбце.
Столбец, на который явно ссылается предикат и отображается в нескольких таблицах с несколькими типами, будет иметь различную колонку в схеме результатов для каждого такого типа. Каждое из названий столбцов будет построено из первоначального имени столбца и типа, разделенного подчеркиваем.

При `project` использовании *ColumnName (ColumnName)* и`:`*columnType*`,` - *ColumnName*-`:`*ColumnType,*..." [`,` `pack(*)`]:
1. Таблица результатов будет включать столбцы, указанные в списке. Если исходная таблица не содержит определенного столбца, значения в соответствующих строках будут нулевыми.
2. При указании *ColumnType* вместе с *ColumnName*этот столбец в **результате** будет иметь данный тип, и значения будут отбрасываться к этому типу, если это необходимо. Обратите внимание, что это не повлияет на тип столбца при оценке *Predicate.*
3. При `pack(*)` использовании остальные столбцы будут упакованы в пакет с недвижимостью `pack_` и появятся в дополнительной колонке

**pack_ столбец**

Этот столбец будет содержать пакет свойств с данными из всех столбцов, которые не отображаются в схеме вывода. Имя исходной колонки будет служить в качестве имени свойства, а значение столбца — значение свойства.

## <a name="predicate-syntax"></a>Предикат Синтаксис

Пожалуйста, обратитесь к [оператору](./whereoperator.md) для некоторых функций фильтрации резюме.

найти оператора поддерживает альтернативный `* has` синтаксис для *термина,* и с помощью только *термин* будет искать термин во всех вхотозаных столбцов

## <a name="notes"></a>Примечания

* Если `project` положение ссылается на столбец, который отображается в нескольких таблицах и имеет несколько типов, тип должен следовать этой ссылке столбца в оговорке проекта
* Если столбец отображается в нескольких `project-smart` таблицах и имеет несколько типов и используется, в результате `find`«в результате» будет соответствующая колонка для каждого типа, как описано в [союзе](./unionoperator.md)
* При использовании *project-smart*изменения в предикате, в исходных таблицах или в схеме таблиц могут привести к изменению схемы вывода. Если необходима постоянная схема результата, используйте *проект*
* `find`область не может включать [функции.](../management/functions.md) Чтобы включить функцию в область поиска - определите [заявление let](./letstatement.md) с ключевым [словом представления](./letstatement.md)

## <a name="performance-tips"></a>Советы по производительности

* Используйте [таблицы](../management/tables.md) в отличие от [табликовых выражений](./tabularexpressionstatements.md)- в `union` случае таблоисного выражения оператор поиска возвращается к запросу, который может привести к деградации производительности
* Если столбец, который отображается в нескольких таблицах и имеет несколько типов, является частью положения проекта, `find` предпочитают добавлять *columnType* в положение проекта об изменении таблицы перед передачей (см. предыдущий совет)
* Добавление фильтров на основе времени в предикат (с помощью значения столбца даты или [ingestion_time()](./ingestiontimefunction.md))
* Предпочитают искать в определенных столбцах в течение полного поиска текста 
* Предпочитаю не ссылаться явно столбцы, которые отосвазаются в нескольких таблицах и имеют несколько типов. Если предикат действителен при разрешении типа таких столбцов для более чем одного типа, запрос будет обратно

[см. примеры](./findoperator.md#examples-of-cases-where-find-will-perform-as-union)

## <a name="examples"></a>Примеры

::: zone pivot="azuredataexplorer"

###  <a name="term-lookup-across-all-tables-in-current-database"></a>Срок поиска во всех таблицах (в текущей базе данных)

Следующий запрос находит все строки из всех таблиц в текущей `Kusto`базе данных, в которой любой столбец включает слово . Полученные записи преобразуются в соответствии с [схемой вывода.](#output-schema) 

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>Срок поиска во всех таблицах, соответствующих шаблону имен (в текущей базе данных)

Следующий запрос находит все строки из всех таблиц в `K`текущей базе данных, `Kusto`имя которых начинается с, и в котором любой столбец включает слово .
Полученные записи преобразуются в соответствии с [схемой вывода.](#output-schema) 

```kusto
find in (K*) where * has "Kusto"
```

###  <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>Срок поиска во всех таблицах во всех базах данных (в кластере)

Следующий запрос находит все строки из всех таблиц во всех `Kusto`базах данных, в которых любой столбец включает слово . Это [запрос запроса перекрестной базы данных.](./cross-cluster-or-database-queries.md) Полученные записи преобразуются в соответствии с [схемой вывода.](#output-schema) 

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>Срок поиска во всех таблицах и базах данных, соответствующих шаблону имен (в кластере)

Следующий запрос находит все строки из всех `K` таблиц, имя которых `B` начинается со всех баз `Kusto`данных, название которых начинается с и в котором любой столбец включает слово . Полученные записи преобразуются в соответствии с [схемой вывода.](#output-schema) 

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>Срок поиска в нескольких кластерах

Следующий запрос находит все строки из всех `K` таблиц, имя которых `B` начинается со всех баз `Kusto`данных, название которых начинается с и в котором любой столбец включает слово . Полученные записи преобразуются в соответствии с [схемой вывода.](#output-schema) 

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>Срок поиска во всех таблицах

Следующий запрос находит все строки из всех таблиц, `Kusto`в которых любой столбец включает слово . Полученные записи преобразуются в соответствии с [схемой вывода.](#output-schema) 

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>Примеры `find` результатов вывода  

Следующие примеры `find` показывают, как можно использовать в двух таблицах: EventsTable1 и EventsTable2. Предположим, что у нас есть следующее содержание этих двух таблиц: 

### <a name="eventstable1"></a>EventsTable1

|Session_Id|Level|EventText|Версия
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Некоторые Text1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Некоторые Text2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Error|Некоторые Text3|v1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|Сведения|Некоторые Text4|v1.1.0

### <a name="eventstable2"></a>EventsTable2

|Session_Id|Level|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Сведения|Некоторые другие Text1|Событие1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Некоторые другие Text2|Событие2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Некоторые другие Text3|Событие3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|Error|Некоторые другие Text4|Событие4


### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>Поиск в общих столбцах, проект общих и необычных столбцов и упаковать остальные  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|Версия|EventName|pack_
|---|---|---|---|---|
|EventsTable1|Некоторые Text2|v1.0.0||"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Уровень":"Ошибка"
|EventsTable2|Некоторые другие Text3||Событие3|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Уровень":"Ошибка"


### <a name="search-in-common-and-uncommon-columns"></a>Поиск в общих и необычных столбцов

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|EventText|Версия|EventName|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Некоторые Text1|v1.0.0
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Некоторые Text2|v1.0.0
|EventsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Некоторые другие Text1||Событие1

Примечание: на практике строки *EventsTable1* будут ```Version == 'v1.0.0'``` отфильтрованы с помощью предиката, а *строки EventsTable2* будут отфильтрованы предикатом. ```EventName == 'Event1'```

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>Используйте сокращенную нотацию для поиска во всех таблицах текущей базы данных

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Level|EventText|pack_|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Некоторые Text1|"Версия":"v1.0.0"
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Некоторые Text2|"Версия":"v1.0.0"
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Сведения|Некоторые другие Text2|«EventName»:«Событие2»
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|Некоторые другие Text3|«EventName»:Event3»


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>Возврат результатов из каждой строки в виде пакета свойств

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTable1|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Уровень":"Информация", "EventText":"Некоторые Text1", "Версия":"v1.0.0"
|EventsTable1|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Уровень":"Ошибка", "EventText":"Некоторые Text2", "Версия":"v1.0.0"
|EventsTable2|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Уровень":"Информация", "EventText":"Некоторые другие Text2", "EventName":"Событие2"
|EventsTable2|"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Уровень":"Ошибка", "EventText":"Некоторые другие Text3", "EventName":"Событие3"


## <a name="examples-of-cases-where-find-will-perform-as-union"></a>Примеры случаев, когда `find``union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>Использование нетабулярного выражения в качестве находки

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>Ссылка на столбец, который отображается в нескольких таблицах и имеет несколько типов

Предположим, что мы создали две таблицы, запустив: 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* Следующий запрос будет выполнен `union`как:
```kusto
find in (Table1, Table2) where ProcessId == 1001
```

Схема результатов вывода будет __(Level:string, Timestamp, ProcessId_string, ProcessId_int)__

* Следующий запрос также будет выполнен как, `union` но даст другую схему результата:
```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

Схема результатов вывода будет __(Level:string, Timestamp, ProcessId_string)__
