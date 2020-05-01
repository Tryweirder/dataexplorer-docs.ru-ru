---
title: STDEV () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается СТАНДОТКЛОН () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a29621a18a364145585022b1f0651100cadab1c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618549"
---
# <a name="stdev-aggregation-function"></a>STDEV () (агрегатная функция)

Вычисляет стандартное отклонение *выражения expr* по группе, принимая в качестве [примера](https://en.wikipedia.org/wiki/Sample_%28statistics%29)группу. 

* Используемая формула:

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="Пример STDEV":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

**Синтаксис**

`stdev(`суммировать *выражение*`)`

**Аргументы**

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

**Возвращает**

Стандартное значение отклонения *выражения* в группе.
 
**Примеры**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1, 2, 3, 4, 5]|1.58113883008419|