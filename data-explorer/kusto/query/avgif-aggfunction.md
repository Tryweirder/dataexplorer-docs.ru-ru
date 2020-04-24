---
title: avgif() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описана функция avgif() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61352be628b7c5a05085c092d0c022deaa0d9b6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518264"
---
# <a name="avgif-aggregation-function"></a>avgif() (функция агрегирования)

Рассчитывает [среднее значение](avg-aggfunction.md) *Expr* по всей группе, `true`для которой *Predicate* оценивается в .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `avgif(` *Expr*`, `*Predicate*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. Записи `null` со значениями игнорируются и не включаются в расчет.
* *Предикат*: Предикат, что если это правда, вычисленное значение *Expr* будет добавлено к среднему.

**Возвращает**

Среднее значение *Expr* по всей группе, `true`где *Predicate* оценивает .
 
**Примеры**

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|