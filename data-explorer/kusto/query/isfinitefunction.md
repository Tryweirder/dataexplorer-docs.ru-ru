---
title: isfinite () - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается изфинит() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a17a39cce91fe039b2cf55cc5c98dba111cc334
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513606"
---
# <a name="isfinite"></a>isfinite()

Возвращает ли вход конечное значение (не является ни бесконечным, ни NaN).

**Синтаксис**

`isfinite(`*X*`)`

**Аргументы**

* *x*: Реальное число.

**Возвращает**

Ненулевое значение (истинное), если x конечно; и ноль (ложный) в противном случае.

**См. также:**

* Для проверки, если значение является нулевым, [см. isnull()](isnullfunction.md).
* Для проверки, если значение бесконечно, [см. isinf()](isinffunction.md).
* Для проверки, является ли значение NaN (Не-номер), см. [isnan()](isnanfunction.md).

**Пример**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|исфинит|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|