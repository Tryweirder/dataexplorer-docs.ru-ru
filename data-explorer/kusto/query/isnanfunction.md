---
title: isnan () - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны isnan() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 123d9cd32d645bb1225983138973a17b6bb9ecf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513572"
---
# <a name="isnan"></a>isnan()

Возвращает значение ввода не номера (NaN).  

**Синтаксис**

`isnan(`*X*`)`

**Аргументы**

* *x*: Реальное число.

**Возвращает**

Ненулевое значение (истинное), если x naN; и ноль (ложный) в противном случае.

**См. также:**

* Для проверки, если значение является нулевым, [см. isnull()](isnullfunction.md).
* Для проверки, если значение является конечным, [см. isfinite()](isfinitefunction.md).
* Для проверки, если значение бесконечно, [см. isinf()](isinffunction.md).

**Пример**

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|