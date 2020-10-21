---
title: VarianceP () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается VarianceP () (статистическая функция) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6cb9ce6ab41e5417d9d39d90d6b7fb31dd8b1fab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251321"
---
# <a name="variancep-aggregation-function"></a>VarianceP () (агрегатная функция)

Вычисляет дисперсию *выражения* в группе, принимая во внимание группу как [совокупность](https://en.wikipedia.org/wiki/Statistical_population). 

* Используемая формула:

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="Заполнение дисперсии":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `variancep(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Значение дисперсии *выражения* в группе.
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2|