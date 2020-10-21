---
title: Оператор as в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор as в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15dcf79938f4b83f18055b6f59a9b70998ab6049
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252814"
---
# <a name="as-operator"></a>Оператор as

Привязывает имя к входному табличному выражению оператора, тем самым позволяя запросу ссылаться на значение табличного выражения несколько раз, не нарушая запрос и привязывая имя с помощью [инструкции Let](letstatement.md).

## <a name="syntax"></a>Синтаксис

*T* `|` `as` [ `hint.materialized` `=` `true` ] *имя*

## <a name="arguments"></a>Аргументы

* *T*: табличное выражение.
* *Имя*: временное имя табличного выражения.
* `hint.materialized`: Если задано `true` значение, то значением табличного выражения будет материализоваться, как если бы оно было заключено в вызов функции [материализации ()](./materializefunction.md) .

> [!NOTE]
> * Имя, заданное параметром, `as` будет использоваться в `withsource=` столбце [Union](./unionoperator.md), `source_` столбце поиска и [find](./findoperator.md) `$table` столбце [поиска](./searchoperator.md).
> * Табличное выражение с именем, использующее оператор в внешних табличных входных данных [объединения](./joinoperator.md) (`$left`), может также использоваться в табличных внутренних входных данных объединения (`$right`).

## <a name="examples"></a>Примеры

```kusto
// 1. In the following 2 example the union's generated TableName column will consist of 'T1' and 'T2'
range x from 1 to 10 step 1 
| as T1 
| union withsource=TableName T2

union withsource=TableName (range x from 1 to 10 step 1 | as T1), T2

// 2. In the following example, the 'left side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Start"
//    and the 'right side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Stop"
MyLogTable  
| where type == "Event"
| as T
| where Name == "Start"
| join (
    T
    | where Name == "Stop"
) on ActivityId
```