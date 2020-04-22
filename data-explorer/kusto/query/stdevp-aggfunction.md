---
title: stdevp() (функция агрегации) - Azure Data Explorer Документы Майкрософт
description: В этой статье описанstdevp() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0549c15ec9e2435d242f210e6dfc2163796e5f39
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663202"
---
# <a name="stdevp-aggregation-function"></a>stdevp() (функция агрегирования)

Рассчитывает стандартное отклонение *Expr* по всей группе, учитывая группу как [популяцию.](https://en.wikipedia.org/wiki/Statistical_population) 

* Подержана формула:

:::image type="content" source="images/aggregations/stdev-population.png" alt-text="Население Стдевов":::

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `stdevp(` *Expr*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 

**Возвращает**

Стандартное значение отклонения *Expr* по всей группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.4142135623731|