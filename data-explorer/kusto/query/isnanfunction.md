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
ms.openlocfilehash: f73effae8d91524f46548d57288a23d79cffd0a5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103279"
---
# <a name="isnan"></a>isnan()

Возвращает, является ли входное значение нечисловым (NaN).  

## <a name="syntax"></a>Синтаксис

`isnan(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число.

## <a name="returns"></a>Результаты

Ненулевое значение (true), если x — NaN; и ноль (false) в противном случае.

## <a name="see-also"></a>См. также раздел

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
|0|0|NaN|1|
|1|-1|-1|0|