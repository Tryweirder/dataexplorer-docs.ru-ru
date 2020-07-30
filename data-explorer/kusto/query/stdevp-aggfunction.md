---
title: STDEVP () (агрегатная функция) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается СТАНДОТКЛОНП () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c5dffc8695df466dfc1ac9f0c5bcc4a40f687b2a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342722"
---
# <a name="stdevp-aggregation-function"></a>STDEVP () (агрегатная функция)

Вычисляет стандартное отклонение *выражения expr* по группе, учитывая группу как [совокупность](https://en.wikipedia.org/wiki/Statistical_population). 

* Используемая формула:

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="Заполнение СТАНДОТКЛОН":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `stdevp(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Стандартное значение отклонения *выражения* в группе.
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1, 2, 3, 4, 5]|1.4142135623731|