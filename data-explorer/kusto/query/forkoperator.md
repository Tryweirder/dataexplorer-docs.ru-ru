---
title: оператор вилки - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается оператор вилки в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 13cd837b3874a55ec758991f5609e089daba7c75
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515204"
---
# <a name="fork-operator"></a>Оператор fork

Параллельно выполняется несколько операторов потребительских товаров.

**Синтаксис**

*T* `|` `=``(``)` `=``(``)` *name**name**subquery* *subquery* - имя - подзака - имя - подзаголовок ... `fork`

**Аргументы**

* *subquery* — это трубопровод ниже по течению операторов запросов
* *имя* — временное название таблицы результатов subquery

**Возвращает**

Несколько таблиц результатов, по одной для каждой из подзапросов.

**Поддерживаемые операторы**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**Примечания**

* [`materialize`](materializefunction.md)функцию можно использовать в [`join`](joinoperator.md) качестве [`union`](unionoperator.md) замены для использования или на вилке ног.
Поток ввода будет кэшироватьматериализироваться, а затем кэшированное выражение может быть использовано в ногах соединения/соединения.

* Имя, приведенное `name` аргументом [`as`](asoperator.md) или с помощью оператора, будет [`Kusto.Explorer`](../tools/kusto-explorer.md) использоваться в качестве для обозначение вкладки результата в инструменте.

* Избегайте `fork` использования с одним подкачно.

* Предпочитаюиспользовать [batch](batches.md) использование [`materialize`](materializefunction.md) пакета с табликными заявлениями выражения по сравнению с `fork` оператором.

**Примеры**

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