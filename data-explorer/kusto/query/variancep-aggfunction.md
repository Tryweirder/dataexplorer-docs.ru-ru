---
title: VarianceP () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается VarianceP () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 80f3f900649d2c4c36c7a50831e011f0ee018860
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618022"
---
# <a name="variancep-aggregation-function"></a>VarianceP () (агрегатная функция)

Вычисляет дисперсию *выражения* в группе, принимая во внимание группу как [совокупность](https://en.wikipedia.org/wiki/Statistical_population). 

* Используемая формула:

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="Заполнение дисперсии":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

`variancep(`суммировать *выражение*`)`

**Аргументы**

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

**Возвращает**

Значение дисперсии *выражения* в группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2|