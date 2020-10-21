---
title: вариативность () (статистическая функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается вариативность () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a031efb5068e4497df0fa7acb54561c3b3caffb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240943"
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