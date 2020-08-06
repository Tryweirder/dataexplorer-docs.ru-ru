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
ms.openlocfilehash: 6511006373cd1f6245a0dcc04537f3994183d63e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803766"
---
# <a name="database-scope-function"></a>Database () (Функция scope)

::: zone pivot="azuredataexplorer"

Изменяет ссылку на запрос на определенную базу данных в области кластера. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

> [!NOTE]
> * Дополнительные сведения см. в разделе [запросы между базами данных и между кластерами](cross-cluster-or-database-queries.md).
> * Сведения о доступе к удаленному кластеру и удаленной базе данных см. в разделе Функция области [Cluster ()](clusterfunction.md) .

## <a name="syntax"></a>Синтаксис

`database(`*стрингконстант*`)`

## <a name="arguments"></a>Аргументы

* *стрингконстант*: имя базы данных, на которую указывает ссылка. Возможно, база данных имеет значение `DatabaseName` или `PrettyName` . Аргумент должен быть _константой_ до выполнения запроса, т. е. не может быть результатом вычисления вложенного запроса.

## <a name="examples"></a>Примеры

### <a name="use-database-to-access-table-of-other-database"></a>Использование базы данных () для доступа к таблице других баз данных

```kusto
database('Samples').StormEvents | count
```

|Count|
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

|Count|
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

> [!NOTE]
> Такие функции можно использовать только локально, а не в запросах между кластерами.

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
