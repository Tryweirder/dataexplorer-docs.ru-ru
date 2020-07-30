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
ms.openlocfilehash: 4222e0672290a6d934382dd6f922aec082a19af7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338683"
---
# <a name="variance-aggregation-function"></a>вариативность () (статистическая функция)

Вычисляет дисперсию *выражения* в группе, принимая в качестве [примера](https://en.wikipedia.org/wiki/Sample_%28statistics%29)группу. 

* Используемая формула:

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="Пример дисперсии":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `variance(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Значение дисперсии *выражения* в группе.
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1, 2, 3, 4, 5]|2.5|