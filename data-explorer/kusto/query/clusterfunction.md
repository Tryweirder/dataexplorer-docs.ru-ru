---
title: Cluster () (Функция scope) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается кластер () (Функция scope) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3e1f74d6605b4916a2718a00fd252141060d748f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348893"
---
# <a name="cluster-scope-function"></a>Cluster () (Функция scope)

::: zone pivot="azuredataexplorer"

Изменяет ссылку на запрос на удаленный кластер. 

```kusto
cluster('help').database('Sample').SomeTable
```

## <a name="syntax"></a>Синтаксис

`cluster(`*стрингконстант*`)`

## <a name="arguments"></a>Аргументы

* *стрингконстант*: имя кластера, на который указывает ссылка. Имя кластера может быть либо полным DNS-именем, либо строкой, которая будет иметь суффикс `.kusto.windows.net` . Аргумент должен быть _константой_ до выполнения запроса, т. е. не может поступать от вычисления вложенного запроса.

**Примечания**

* Для доступа к базе данных в пределах одной и той же функции USE [Database ()](databasefunction.md) .
* Дополнительные сведения о запросах между кластерами и между базами данных доступны [здесь](cross-cluster-or-database-queries.md)  

## <a name="examples"></a>Примеры

### <a name="use-cluster-to-access-remote-cluster"></a>Использование кластера () для доступа к удаленному кластеру 

Следующий запрос можно запустить на любом из кластеров Kusto.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Счетчик|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Использование Cluster () в инструкциях Let 

Тот же запрос, который приведен выше, можно переписывать, чтобы использовать встроенную функцию (оператор Let), которая получает параметр, `clusterName` который передается в функцию Cluster ().

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|Счетчик|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>Использование Cluster () в функциях 

Тот же запрос, который приведен выше, можно переписывать для использования в функции, принимающей параметр, `clusterName` который передается в функцию Cluster ().

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**Примечание.** такие функции можно использовать только локально, а не в запросах между кластерами.

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
