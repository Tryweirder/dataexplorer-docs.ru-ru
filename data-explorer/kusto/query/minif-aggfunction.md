---
title: minif() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны minif() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0aad254ec01e83bdb07734e5b309c1450512b446
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512365"
---
# <a name="minif-aggregation-function"></a>миниф() (функция агрегирования)

Возвращает минимальное значение по всей группе, `true`для которой *Predicate* оценивается в .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

Смотрите также - [мин()](min-aggfunction.md) функция, которая возвращает минимальное значение по всей группе без выражения предиката.

**Синтаксис**

`summarize``minif(` *Expr*`,`*Предикат*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации.
* *Предикат*: Предикат, что если это правда, вычисленное значение *Expr* будет проверено на минимум.

**Возвращает**

Минимальное значение *Expr* по всей группе, `true`для которой *Predicate* оценивается в .

**Примеры**

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|