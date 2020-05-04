---
title: Database () (Функция scope) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается база данных () (Функция scope) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e3f874ecfc0bb1872f08efa3269c73b02971e4f3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737646"
---
# <a name="database-scope-function"></a>Database () (Функция scope)

::: zone pivot="azuredataexplorer"

Изменяет ссылку на запрос на определенную базу данных в области кластера. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

**Синтаксис**

`database(`*стрингконстант*`)`

**Аргументы**

* *стрингконстант*: имя базы данных, на которую указывает ссылка. Возможно, база данных имеет `DatabaseName` значение `PrettyName`или. Аргумент должен быть _константой_ до выполнения запроса, т. е. не может быть результатом вычисления вложенного запроса.

**Примечания**

* Сведения о доступе к удаленному кластеру и удаленной базе данных см. в разделе Функция области [Cluster ()](clusterfunction.md) .
* Дополнительные сведения о запросах между кластерами и между базами данных доступны [здесь](cross-cluster-or-database-queries.md)

## <a name="examples"></a>Примеры

### <a name="use-database-to-access-table-of-other-database"></a>Используйте базу данных () для доступа к таблице другой базы данных. 

```kusto
database('Samples').StormEvents | count
```

|Счетчик|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>Использование базы данных () внутри инструкций Let 

Тот же запрос, который приведен выше, может быть переписан для использования встроенной функции (оператор Let), принимающей параметр `dbName` , который передается в функцию Database ().

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|Счетчик|
|---|
|59066|

### <a name="use-database-inside-functions"></a>Использование базы данных () в функциях 

Тот же запрос, который приведен выше, можно переписывать для использования в функции, принимающей параметр `dbName` , который передается в функцию Database ().

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**Примечание.** такие функции можно использовать только локально, а не в запросах между кластерами.

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
