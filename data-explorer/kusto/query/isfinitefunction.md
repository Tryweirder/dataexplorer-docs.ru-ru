---
title: с ограничением () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются ограничения () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b428d43afd7984bbcf19351da702517a3a1244a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242303"
---
# <a name="isfinite"></a>isfinite()

Возвращает значение, указывающее, является ли вход конечным значением (не является ни бесконечным, ни NaN).

## <a name="syntax"></a>Синтаксис

`isfinite(`*x*`)`

## <a name="arguments"></a>Аргументы

* *x*: вещественное число.

## <a name="returns"></a>Результаты

Ненулевое значение (true), если x является конечным; и ноль (false) в противном случае.

## <a name="see-also"></a>См. также раздел

* Для проверки, имеет ли значение null, см. раздел [IsNull ()](isnullfunction.md).
* Чтобы проверить, является ли значение бесконечным, см. раздел [исинф ()](isinffunction.md).
* Для проверки, является ли значение NaN (нечисловое), см. раздел [isNaN ()](isnanfunction.md).

## <a name="example"></a>Пример

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isFinite|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|не число|0|
|1|0|∞|0|