---
title: как оператор - Azure Data Explorer Документы Майкрософт
description: В этой статье оператор описывается как оператор в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05dc96fb7eec773d1e55d8b94a33cdda928622ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518434"
---
# <a name="as-operator"></a>Оператор as

Связывает имя с входной табулярным выражением оператора, что позволяет запросу ссылаться на значение табличныго выражения несколько раз, не нарушая запрос и связывая имя через [оператор](letstatement.md)let.

**Синтаксис**

*T* `|` Т `as` `hint.materialized` `=` з `true` *именем*

**Аргументы**

* *T*: Табулярное выражение.
* *Имя*: Временное название табулярного выражения.
* `hint.materialized`: Если `true`установить, значение табловидного выражения будет материализовано, как если бы он был обернут [материализовать ()](./materializefunction.md) вызов функции.

**Примечания**

* Название, данное `as` будет использоваться `withsource=` в колонке `source_` [союза,](./unionoperator.md)колонке `$table` [находки,](./findoperator.md)и колонке [поиска.](./searchoperator.md)

* Табличное выражение с именем, использующее оператор в внешних табличных входных данных [объединения](./joinoperator.md) (`$left`), может также использоваться в табличных внутренних входных данных объединения (`$right`).

**Примеры**

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