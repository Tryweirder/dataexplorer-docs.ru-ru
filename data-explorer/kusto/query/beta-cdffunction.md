---
title: beta_cdf () — обозреватель данных Azure
description: В этой статье описывается beta_cdf () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16365c7445ca5436098028cbfbe5709e5b425a38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253109"
---
# <a name="beta_cdf"></a>beta_cdf()

Возвращает стандартную функцию интегрального бета-распределения.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

Если *вероятность*  =  `beta_cdf(` *x*,... `)` , то `beta_inv(` *вероятность*,... `)`  =  *x*.

Бета-распределение обычно используется для изучения вариативности в процентном отношении чего-либо в образцах, например часть дня, которую люди проводят перед телевизором.

## <a name="syntax"></a>Синтаксис

`beta_cdf(`*x* `, ` *альфа-канал* `, ` *бета-версия*`)`

## <a name="arguments"></a>Аргументы

* *x*: значение, для которого вычисляется функция.
* *альфа*— параметр распределения.
* *бета*— параметр распределения.

## <a name="returns"></a>Результаты

* [Интегральная функция бета-распределения](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function).

**Примечания**

Если любой из аргументов является нечисловым, beta_cdf () возвращает значение null.

Если x < 0 или x > 1, то beta_cdf () возвращает значение NaN.

Если альфа ≤ 0 или Alpha > 10000, beta_cdf () возвращает значение NaN.

Если бета ≤ 0 или бета-> 10000, beta_cdf () возвращает значение NaN.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.9, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_cdf(x, alpha, beta)
```

|x|alpha|бета-версия|comment|b|
|---|---|---|---|---|
|0,9|10|20|Допустимые входные данные|0.999999999999959|
|1.5|10|20|x > 1, возвращает NaN|не число|
|–10|10|20|x < 0, возвращает NaN|не число|
|0,1|-1|20|Альфа-< 0, возвращает NaN|не число|


## <a name="see-also"></a>См. также раздел


* Вычисление обратной функции бета-интегральной плотности см. в статье [Beta – Inv ()](./beta-invfunction.md).
* Сведения о плотности вероятности вычислений см. в разделе [Beta-PDF ()](./beta-pdffunction.md).
