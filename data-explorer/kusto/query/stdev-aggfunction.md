---
title: stdev() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описанstdev() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d54af583db6f7aca0b436040c453249207a5ae59
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663096"
---
# <a name="stdev-aggregation-function"></a>stdev() (функция агрегирования)

Высчитывает стандартное отклонение *Expr* по всей группе, рассматривая группу как [образец.](https://en.wikipedia.org/wiki/Sample_%28statistics%29) 

* Подержана формула:

:::image type="content" source="images/aggregations/stdev-sample.png" alt-text="Образец Stdev":::

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `stdev(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Стандартное значение отклонения *Expr* по всей группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.58113883008419|