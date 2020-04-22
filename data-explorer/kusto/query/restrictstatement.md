---
title: Ограничьте оператора - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описано заявление об ограничении в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: cbd21c01956f817c5db19a93104028dba2b2b2b4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765985"
---
# <a name="restrict-statement"></a>Инструкция restrict

::: zone pivot="azuredataexplorer"

Оператор ограничения ограничивает набор сущностей таблицы/просмотра, которые видны инструкциям запроса, которые следуют за ним. Например, в базе данных,`A` `B`которая включает две таблицы (, ) `B` приложение может предотвратить доступ к `A` остальной части запроса и только "видеть" ограниченную форму таблицы с помощью представления.

Основной сценарий оператора ограничения предназначен для приложений среднего уровня, которые принимают запросы от пользователей и хотят применить механизм безопасности уровня строки к этим запросам. Приложение среднего уровня может префиксировать запрос пользователя **с логической моделью,** набором инструкций, определяющих представления, `T | where UserId == "..."`которые ограничивают доступ пользователя к данным (например, ). При добавлении последнего оператора он ограничивает доступ пользователя только к логической модели.

**Синтаксис**

`restrict``access` `,` -*EntitySpecifier* `to` `(``)`

Где *EntitySpecifier* является одним из:
* Идентификатор, определяемый заявлением let как таблик-представление.
* Ссылка на таблицу (по аналогии с той, которая используется в заявлении профсоюза).
* Шаблон, определяемый декларацией шаблонов.

Все таблицы, табловые представления или шаблоны, не указанные в отчете о ограничении, становятся «невидимыми» для остальной части запроса. 

**Примечания**

Заявление ограничения может быть использовано для ограничения доступа к объектам в другой базе данных или кластере (подстановочные карты не поддерживаются в именах кластеров).

**Аргументы**

Заявление ограничения может получить один или несколько параметров, определяющих разрешительное ограничение при разрешении имени объекта. Сущность может быть:
- [пусть заявление,](./letstatement.md) появляющееся перед `restrict` заявлением. 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- [Таблицы](../management/tables.md) или [функции,](../management/functions.md) определенные в метаданных базы данных.

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- Шаблоны Wildcard, которые могут соответствовать кратным [инструкциям](./letstatement.md) или таблицам/функциям  

```kusto
let Test1 = () { print x=1 };
let Test2 = () { print y=1 };
restrict access to (*);
// Now access is restricted to Test1, Test2 and no tables/functions are accessible.

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database().*);
// Now access is restricted to all tables/functions of the current database ('DB2' is not accessible).

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database('DB2').*);
// Now access is restricted to all tables/functions of the database 'DB2'
```


**Примеры**

В следующем примере показано, как приложение среднего уровня может подготовить запрос пользователя с помощью логической модели, которая не позволяет пользователю загонять данные любого другого пользователя.

```kusto
// Assume the database has a single table, UserData,
// with a column called UserID and other columns that hold
// per-user private information.
//
// The middle-tier application generates the following statements.
// Note that "username@domain.com" is something the middle-tier application
// derives per-user as it authenticates the user.
let RestrictedData = view () { Data | where UserID == "username@domain.com" };
restrict access to (RestrictedData);
// The rest of the query is something that the user types.
// This part can only reference RestrictedData; attempting to reference Data
// will fail.
RestrictedData | summarize IrsLovesMe=sum(Salary) by Year, Month
```

```kusto
// Restricting access to Table1 in the current database (database() called without parameters)
restrict access to (database().Table1);
Table1 | count

// Restricting acess to Table1 in the current database and Table2 in database 'DB2'
restrict access to (database().Table1, database('DB2').Table2);
union 
    (Table1),
    (database('DB2').Table2))
| count

// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
