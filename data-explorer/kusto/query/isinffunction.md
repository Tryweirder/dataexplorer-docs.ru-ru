---
title: isinf() - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны isinf() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d93697890ee05cabf9ca1830ac047d90d8c9e844
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513589"
---
# <a name="isinf"></a>isinf()

Возвращает ли вход новейшом уликнутовом (положительном или отрицательном) значением.  

**Синтаксис**

`isinf(`*X*`)`

**Аргументы**

* *x*: Реальное число.

**Возвращает**

Ненулевое значение (истинное), если x является положительным или отрицательным бесконечным; и ноль (ложный) в противном случае.

**См. также:**

* Для проверки, если значение является нулевым, [см. isnull()](isnullfunction.md).
* Для проверки, если значение является конечным, [см. isfinite()](isfinitefunction.md).
* Для проверки, является ли значение NaN (Не-номер), см. [isnan()](isnanfunction.md).

**Пример**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|NaN|0|
|1|0|∞|1|
