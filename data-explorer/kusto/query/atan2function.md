---
title: atan2 () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается atan2 () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1229ff1476afe2863f07cfc0ff7aecffadd5867f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252761"
---
# <a name="atan2"></a>atan2()

Вычисляет угол в радианах между положительной осью x и лучом от начала до точки (y, x).

## <a name="syntax"></a>Синтаксис

`atan2(`по *оси y* `,` *x*`)`

## <a name="arguments"></a>Аргументы

* Координата *x*: x (вещественное число).
* *y*: координата y (вещественное число).

## <a name="returns"></a>Результаты

* Угол в радианах между положительной осью x и лучом от начала до точки (y, x).

## <a name="examples"></a>Примеры

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3,14159265358979)|-1,5707963267949|