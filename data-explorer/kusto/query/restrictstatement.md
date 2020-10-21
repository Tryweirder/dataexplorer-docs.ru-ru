---
title: Предложение restrict — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается инструкция restrict в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f510e62e6b1ad828f0e132bbad214dc7119f8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243019"
---
# <a name="restrict-statement"></a>Инструкция restrict

::: zone pivot="azuredataexplorer"

Инструкция restrict ограничивает набор сущностей таблицы или представлений, которые видимы для операторов запроса, которые следуют за ним. Например, в базе данных, которая содержит две таблицы ( `A` , `B` ), приложение может препятствовать доступу остальным запросам `B` и только «видеть» ограниченную форму таблицы с `A` помощью представления.

Основной сценарий оператора restrict предназначен для приложений среднего уровня, которые принимают запросы от пользователей и хотят применять механизм безопасности на уровне строк для этих запросов. Приложение среднего уровня может добавить префикс к запросу пользователя с помощью **логической модели**, набора инструкций let, определяющих представления, которые ограничивают доступ пользователя к данным (например, `T | where UserId == "..."` ). При добавлении последней инструкции она будет ограничивать доступ пользователя только к логической модели.

> [!NOTE]
> Инструкцию restrict можно использовать для ограничения доступа к сущностям в другой базе данных или кластере (в именах кластеров не поддерживаются подстановочные знаки).

## <a name="syntax"></a>Синтаксис

`restrict``access` `to` `(` [*ЕнтитиспеЦифиер* [ `,` ...]]`)`

Где *ентитиспеЦифиер* является одним из следующих:
* Идентификатор, определяемый инструкцией let в виде табличного представления.
* Ссылка на таблицу (аналогично той, которая используется инструкцией UNION).
* Шаблон, определенный объявлением шаблона.

Все таблицы, табличные представления или шаблоны, не заданные оператором restrict, становятся «невидимыми» остальной части запроса. 

## <a name="arguments"></a>Аргументы

Инструкция restrict может получить один или несколько параметров, определяющих ограничение разрешений во время разрешения имен сущности. Сущность может быть:
* [инструкция Let](./letstatement.md) появлялась до `restrict` оператора. 

  ```kusto
  // Limit access to 'Test' let statement only
  let Test = () { print x=1 };
  restrict access to (Test);
  ```

* [Таблицы](../management/tables.md) или [функции](../management/functions.md) , определенные в метаданных базы данных.

    ```kusto
    // Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
    // and other database 'DB2' has Table2 defined in the metadata
    
    restrict access to (database().Table1, database().Func1, database('DB2').Table2);
    ```

* Шаблоны с подстановочными знаками, которые могут соответствовать нескольким [операторам Let](./letstatement.md) , таблицам и функциям  

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

## <a name="examples"></a>Примеры

В следующем примере показано, как приложение среднего уровня может добавить в начало запроса пользователя логическую модель, которая не позволяет пользователю запрашивать данные других пользователей.

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

// Restricting access to Table1 in the current database and Table2 in database 'DB2'
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

Эта возможность не поддерживается в Azure Monitor

::: zone-end
