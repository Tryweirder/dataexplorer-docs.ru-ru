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
ms.openlocfilehash: baafca4d8d5711d55838bceae817c36ecb0edd6f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618583"
---
# <a name="stdevp-aggregation-function"></a>STDEVP () (агрегатная функция)

Вычисляет стандартное отклонение *выражения expr* по группе, учитывая группу как [совокупность](https://en.wikipedia.org/wiki/Statistical_population). 

* Используемая формула:

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="Заполнение СТАНДОТКЛОН":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

`stdevp(`суммировать *выражение*`)`

**Аргументы**

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

**Возвращает**

Стандартное значение отклонения *выражения* в группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1, 2, 3, 4, 5]|1.4142135623731|