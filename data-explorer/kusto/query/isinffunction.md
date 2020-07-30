---
title: исинф () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается исинф () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 71a37d7a1bd700b5f929c009197a382315099e08
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347244"
---
# <a name="isinf"></a>isinf()

Возвращает, является ли входное значение бесконечным (положительным или отрицательным).  

## <a name="syntax"></a>Синтаксис

`isinf(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число.

## <a name="returns"></a>Результаты

Ненулевое значение (true), если x является положительным или отрицательным бесконечным; и ноль (false) в противном случае.

**См. также:**

* Для проверки, имеет ли значение null, см. раздел [IsNull ()](isnullfunction.md).
* Чтобы проверить, является ли значение конечным, см. раздел с [ограничением ()](isfinitefunction.md).
* Для проверки, является ли значение NaN (нечисловое), см. раздел [isNaN ()](isnanfunction.md).

## <a name="example"></a>Пример

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|Не число|0|
|1|0|∞|1|
