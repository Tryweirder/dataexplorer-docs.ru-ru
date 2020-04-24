---
title: дисперсия() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается дисперсия () (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b1d2ea47060ecede855a3fb419bbbfe2bf0b538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504715"
---
# <a name="variance-aggregation-function"></a>дисперсия() (функция агрегирования)

Высчитывает дисперсию *Expr* по всей группе, рассматривая группу как [образец.](https://en.wikipedia.org/wiki/Sample_%28statistics%29) 

* Подержана формула: ![альт текст](./images/aggregations/variance-sample.png "дисперсия-образец")

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `variance(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Значение дисперсии *Expr* по всей группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2.5|