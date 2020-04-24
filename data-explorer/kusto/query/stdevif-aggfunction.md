---
title: stdevif() (функция агрегации) - Azure Data Explorer Документы Майкрософт
description: В этой статье описанstdevif() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4a64cf1bb69860a2a8bd64de91cb00c2f0ec296f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506976"
---
# <a name="stdevif-aggregation-function"></a>stdevif() (функция агрегирования)

Вычисляет [stdev](stdev-aggfunction.md) *Expr* по всей группе, `true`для которой *Predicate* оценивает .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `stdevif(` *Expr*`, `*Predicate*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 
* *Предикат*: Предикат, что если это правда, вычисленное значение *Expr* будет добавлено к стандартному отклонению.

**Возвращает**

Стандартное значение отклонения *Expr* по всей группе, где *Predicate* оценивает . `true`
 
**Примеры**

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|