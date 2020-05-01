---
title: вариативность () (статистическая функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается вариативность () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f8afae2413d65618cf6b6e2f400df2500b06078
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618057"
---
# <a name="variance-aggregation-function"></a>вариативность () (статистическая функция)

Вычисляет дисперсию *выражения* в группе, принимая в качестве [примера](https://en.wikipedia.org/wiki/Sample_%28statistics%29)группу. 

* Используемая формула:

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="Пример дисперсии":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

`variance(`суммировать *выражение*`)`

**Аргументы**

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

**Возвращает**

Значение дисперсии *выражения* в группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2.5|