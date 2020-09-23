---
title: с ограничением () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются ограничения () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d1f70675a1f455c6cd0c392483eb574867088394
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103293"
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
|0|0|NaN|0|
|1|0|∞|0|