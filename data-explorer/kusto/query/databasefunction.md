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
ms.openlocfilehash: 1bfe42e18cfe0bb424e933b266eb9861c7676cea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348587"
---
# <a name="database-scope-function"></a>Database () (Функция scope)

::: zone pivot="azuredataexplorer"

Изменяет ссылку на запрос на определенную базу данных в области кластера. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

## <a name="syntax"></a>Синтаксис

`database(`*стрингконстант*`)`

## <a name="arguments"></a>Аргументы

* *стрингконстант*: имя базы данных, на которую указывает ссылка. Возможно, база данных имеет значение `DatabaseName` или `PrettyName` . Аргумент должен быть _константой_ до выполнения запроса, т. е. не может быть результатом вычисления вложенного запроса.

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

Тот же запрос, который приведен выше, может быть переписан для использования встроенной функции (оператор Let), принимающей параметр, `dbName` который передается в функцию Database ().

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

Тот же запрос, который приведен выше, можно переписывать для использования в функции, принимающей параметр, `dbName` который передается в функцию Database ().

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
