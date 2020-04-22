---
title: кластер() (функция области) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается кластер (функция области) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c5f537b47006af4035c9db26388c1d4110c69b55
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766105"
---
# <a name="cluster-scope-function"></a>кластер() (функция области)

::: zone pivot="azuredataexplorer"

Изменяет ссылку запроса на удаленный кластер. 

```kusto
cluster('help').database('Sample').SomeTable
```

**Синтаксис**

`cluster(`*stringConstant*`)`

**Аргументы**

* *stringConstant*: Название кластера, на которое ссылаются. Название кластера может быть либо полностью квалифицированным dNS-именем, либо строкой, которая будет суффиксирована с `.kusto.windows.net`. Аргумент должен быть _постоянным_ до выполнения запроса, т.е. не может исходить от подзапросной оценки.

**Примечания**

* Для доступа к базе данных в рамках одного кластера - используйте функцию [базы данных()](databasefunction.md)
* Более подробная информация о межкластерных и кросс-базах данных запросов доступна [здесь](cross-cluster-or-database-queries.md)  

## <a name="examples"></a>Примеры

### <a name="use-cluster-to-access-remote-cluster"></a>Используйте кластер () для доступа к удаленному кластеру 

Следующий запрос можно запустить на любом из кластеров Kusto.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Использование кластера () внутри впустительных инструкций 

Тот же запрос, что и выше, может быть переписан для `clusterName` использования вневодной функции (let statement), которая получает параметр, который передается в функцию кластера()

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>Использование кластера () внутри функций 

Тот же запрос, что и выше, может быть переписан `clusterName` для использования в функции, которая получает параметр, который передается в функцию кластера()

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**Примечание:** такие функции могут использоваться только локально, а не в запросе кросс-кластера.

::: zone-end

::: zone pivot="azuremonitor"

Это не поддерживается в Azure Monitor

::: zone-end
