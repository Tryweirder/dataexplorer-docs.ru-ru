---
title: dcountif() (функция агрегирования) - Azure Data Explorer Документы Майкрософт
description: В этой статье описано dcountif() (функция агрегирования) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1cc3c17474db835f381cac32d6107acb312c3d11
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516054"
---
# <a name="dcountif-aggregation-function"></a>dcountif() (функция агрегирования)

Возвращает оценку количества различных значений *Expr* строк, для которых *Predicate* оценивается. `true` 

* Может быть использован только в контексте агрегации внутри [суммировать](summarizeoperator.md).

Читайте о [точности оценки](dcount-aggfunction.md#estimation-accuracy).

**Синтаксис**

суммировать `dcountif(` *Expr*, *Предикат*, -`,` *Точность*`)`

**Аргументы**

* *Expr*: Выражение, которое будет использоваться для расчета агрегации.
* *Предикат*: Выражение, которое будет использоваться для фильтрации строк.
* Приоритет между скоростью и точностью контролирует необязательный параметр *Accuracy*.
    * `0` — наименее точное и самое быстрое вычисление. 1.6% ошибка
    * `1`по умолчанию, который уравновешивает точность и время расчета; около 0,8% погрешности.
    * `2`- точный и медленный расчет; около 0,4% погрешности.
    * `3`- дополнительный точный и медленный расчет; около 0,28% погрешности.
    * `4`- супер точный и медленный расчет; около 0,2% погрешности.
    
**Возвращает**

Возвращает оценку количества различных значений *Expr* строк, для которых *Predicate* оценивает в `true` группе. 

**Пример**

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**Совет: Ошибка смещения**

`dcountif()`может привести к одноразовой ошибке в крайних случаях, когда проходят все `Predicate` строки, или ни один из строк не проходит, выражение