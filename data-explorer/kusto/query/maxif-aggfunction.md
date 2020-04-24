---
title: maxif() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны maxif() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9be25615f9da61aec6b4d56543f624fa0c24c1c4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512450"
---
# <a name="maxif-aggregation-function"></a>maxif() (функция агрегирования)

Возвращает максимальное значение по всей группе, `true`для которой *Predicate* оценивается в .

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md)

Смотрите также - [max()](max-aggfunction.md) функция, которая возвращает максимальное значение по всей группе без выражения предиката.

**Синтаксис**

`summarize``maxif(` *Expr*`,`*Предикат*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации. 
* *Предикат*: Предикат, что если это правда, вычисленное значение *Expr* будет проверено на максимум.

**Возвращает**

Максимальное значение *Expr* по всей группе, `true`для которой *Predicate* оценивается в .

**Примеры**

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|