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
ms.openlocfilehash: 03753cf7dcabb7637335d3dc71f0b9bca773e710
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030452"
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

|Count|
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

|Count|
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

Это не поддерживается в Azure Monitor

::: zone-end
