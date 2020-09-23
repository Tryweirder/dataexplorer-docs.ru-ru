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
ms.openlocfilehash: e9fa24aeec2cc70b41e1fcd4f9d1185ef80b714b
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103232"
---
# <a name="isinf"></a>isinf()

Возвращает, является ли входное значение бесконечным (положительным или отрицательным).  

## <a name="syntax"></a>Синтаксис

`isinf(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число.

## <a name="returns"></a>Результаты

Ненулевое значение (true), если x является положительным или отрицательным бесконечным; и ноль (false) в противном случае.

## <a name="see-also"></a>См. также раздел

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
|0|0|NaN|0|
|1|0|∞|1|
