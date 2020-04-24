---
title: дисперсия() (функция агрегации) - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны дисперсии () (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 386244806a6fcb3f321eb1a6b40595dc71b2b413
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504596"
---
# <a name="variancep-aggregation-function"></a>дисперсия() (функция агрегирования)

Высчитывает дисперсию *Expr* по всей группе, учитывая группу как [население.](https://en.wikipedia.org/wiki/Statistical_population) 

* Подержана формула: ![альт текст](./images/aggregations/variance-population.png "дисперсия-население")

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `variancep(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Значение дисперсии *Expr* по всей группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2|