---
title: isNaN () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается isNaN () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5597f21d5e426329e2793978a6b207efc3868d13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347227"
---
# <a name="isnan"></a>isnan()

Возвращает, является ли входное значение нечисловым (NaN).  

## <a name="syntax"></a>Синтаксис

`isnan(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число.

## <a name="returns"></a>Результаты

Ненулевое значение (true), если x — NaN; и ноль (false) в противном случае.

**См. также:**

* Для проверки, имеет ли значение null, см. раздел [IsNull ()](isnullfunction.md).
* Чтобы проверить, является ли значение конечным, см. раздел с [ограничением ()](isfinitefunction.md).
* Чтобы проверить, является ли значение бесконечным, см. раздел [исинф ()](isinffunction.md).

## <a name="example"></a>Пример

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|IsNaN|
|---|---|---|---|
|-1|1|-1|0|
|0|0|Не число|1|
|1|-1|-1|0|