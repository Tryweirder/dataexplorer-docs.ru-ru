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
ms.openlocfilehash: 4531bd498cf9d9053d57d8b463c7b0adfcc9bf8d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663989"
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

:::image type="content" source="images/aggregations/arg-min.png" alt-text="Арг мин":::
