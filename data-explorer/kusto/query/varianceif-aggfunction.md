---
title: дисперсия() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны дисперсии () (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9dfebb3796f07dec6c91d36d788a018f84f70961
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504681"
---
# <a name="varianceif-aggregation-function"></a>дисперсия() (функция агрегирования)

Вычисляет [дисперсию](variance-aggfunction.md) *Expr* по всей группе, `true`для которой *Predicate* оценивает .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

**Синтаксис**

резюмировать `varianceif(` *Expr*`, `*Predicate*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 
* *Предикат*: Предикат, что если это правда, вычисленное значение *Expr* будет добавлено к дисперсии.

**Возвращает**

Значение дисперсии *Expr* по всей группе, `true`где *Predicate* оценивает .
 
**Примеры**

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|