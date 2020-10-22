---
title: Оператор ветвления — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается оператор ветвления в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c20cb3311a254f0154efa01c9d155cd6352c6ef
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356560"
---
# <a name="fork-operator"></a>Оператор fork

Параллельно запускает несколько операторов-получателей.

## <a name="syntax"></a>Синтаксис

*T* `|` `fork` [*имя* `=` ] `(` *вложенный запрос* `)` [*имя* `=` ] `(` *вложенный запрос* `)` ...

## <a name="arguments"></a>Аргументы

* *вложенный запрос* является нисходящим конвейером операторов запроса
* *имя* является временным именем для таблицы результатов вложенного запроса

## <a name="returns"></a>Результаты

Несколько результирующих таблиц, по одной для каждого вложенного запроса.

**Поддерживаемые операторы**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`project-keep`](project-keep-operator.md), [`project-rename`](projectrenameoperator.md), [`project-reorder`](projectreorderoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**Примечания**

* [`materialize`](materializefunction.md) функцию можно использовать в качестве замены при использовании [`join`](joinoperator.md) или [`union`](unionoperator.md) при обрезке разветвления.
Входной поток будет кэшироваться материализации, а затем кэшированное выражение можно использовать в обрезке соединения и объединения.

* Имя, заданное `name` аргументом или [`as`](asoperator.md) оператором using, будет использоваться в качестве имени для вкладки результатов в [`Kusto.Explorer`](../tools/kusto-explorer.md) инструменте.

* Старайтесь не использовать `fork` с одним вложенным запросом.

* Предпочитать использование [пакета](batches.md) с операторами [`materialize`](materializefunction.md) табличных выражений над `fork` оператором.

## <a name="examples"></a>Примеры

```kusto
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 )
    ( project Timestamp, EventText | top 1000 by Timestamp desc)
    ( summarize min(Timestamp), max(Timestamp) by ActivityID )
 
// In the following examples the result tables will be named: Errors, EventsTexts and TimeRangePerActivityID
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 | as Errors )
    ( project Timestamp, EventText | top 1000 by Timestamp desc | as EventsTexts )
    ( summarize min(Timestamp), max(Timestamp) by ActivityID | as TimeRangePerActivityID )
    
 KustoLogs
| where Timestamp > ago(1h)
| fork
    Errors = ( where Level == "Error" | project EventText | limit 100 )
    EventsTexts = ( project Timestamp, EventText | top 1000 by Timestamp desc )
    TimeRangePerActivityID = ( summarize min(Timestamp), max(Timestamp) by ActivityID )
```