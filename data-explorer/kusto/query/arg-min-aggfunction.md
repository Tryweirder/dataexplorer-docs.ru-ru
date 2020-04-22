---
title: arg_min() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается arg_min (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 58c590e124b01166ad07aa2b00fe865546947f96
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030499"
---
# <a name="arg_min-aggregation-function"></a>arg_min () (функция агрегирования)

Находит строку в группе, которая сводит к минимуму *ExprToMinimize* `*` и возвращает значение *ExprToReturn* (или вернуть всю строку).

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

`summarize``(`*NameExprtominimize* `,` *NameexprToreturn* `,` `)=` `arg_min` `(` *ExprTominimize*, `*`  |  *ExprToReturn* `,``)`

**Аргументы**

* *ExprToMinimize*: Выражение, которое будет использоваться для расчета агрегации. 
* *ExprToReturn*: Выражение, которое будет использоваться для возврата значения, когда *ExprToMinimize* является минимальным. Выражение возврата может быть подстановочным знаком (кв.) для возврата всех столбцов таблицы ввода.
* *NameExprToMinimize*: Дополнительное название для столбца результата, представляющего *ExprToMinimize.*
* *NameExprToReturn*: Дополнительные дополнительные имена для столбцов результатов, представляющих *ExprToReturn*.

**Возвращает**

Находит строку в группе, которая сводит к минимуму *ExprToMinimize* `*` и возвращает значение *ExprToReturn* (или вернуть всю строку).

**Примеры**

Показать самый дешевый поставщик каждого продукта:

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

Показать все детали, а не только имя поставщика:

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

Найти самый южный город на каждом континенте, со своей страной:

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/arg-min-aggfunction/arg-min.png" alt-text="Арг мин":::
