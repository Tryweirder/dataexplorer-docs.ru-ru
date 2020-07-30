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
ms.openlocfilehash: 70118746a079d76b1b6729bed3aae96c48399538
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338666"
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

## <a name="returns"></a>Возвращаемое значение

Значение дисперсии *выражения* в группе.
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2|