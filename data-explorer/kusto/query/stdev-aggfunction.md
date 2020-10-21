---
title: STDEV () (агрегатная функция) — обозреватель данных Azure | Документация Майкрософт
description: В этой статье описывается СТАНДОТКЛОН () (статистическая функция) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d2ed328930f01d3f9c52a675eab61805fd87593
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247065"
---
# <a name="stdev-aggregation-function"></a>STDEV () (агрегатная функция)

Вычисляет стандартное отклонение *выражения expr* по группе, принимая в качестве [примера](https://en.wikipedia.org/wiki/Sample_%28statistics%29)группу. 

* Используемая формула:

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="Пример STDEV":::

* Может использоваться только в контексте агрегирования внутри [сводки](summarizeoperator.md)

## <a name="syntax"></a>Синтаксис

суммировать `stdev(` *выражение*`)`

## <a name="arguments"></a>Аргументы

* *Expr*: выражение, которое будет использоваться для вычисления агрегата. 

## <a name="returns"></a>Результаты

Стандартное значение отклонения *выражения* в группе.
 
## <a name="examples"></a>Примеры

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1, 2, 3, 4, 5]|1.58113883008419|