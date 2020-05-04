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
ms.openlocfilehash: 092915c08b4b3d1e72722a4303e911403b2defd2
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737204"
---
# <a name="cluster-scope-function"></a>Cluster () (Функция scope)

::: zone pivot="azuredataexplorer"

Изменяет ссылку на запрос на удаленный кластер. 

```kusto
cluster('help').database('Sample').SomeTable
```

**Синтаксис**

`cluster(`*стрингконстант*`)`

**Аргументы**

* *стрингконстант*: имя кластера, на который указывает ссылка. Имя кластера может быть либо полным DNS-именем, либо строкой, которая будет иметь суффикс `.kusto.windows.net`. Аргумент должен быть _константой_ до выполнения запроса, т. е. не может поступать от вычисления вложенного запроса.

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

Тот же запрос, который приведен выше, можно переписывать, чтобы использовать встроенную функцию (оператор Let), `clusterName` которая получает параметр, который передается в функцию Cluster ().

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

Тот же запрос, который приведен выше, можно переписывать для использования в функции, принимающей параметр `clusterName` , который передается в функцию Cluster ().

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
