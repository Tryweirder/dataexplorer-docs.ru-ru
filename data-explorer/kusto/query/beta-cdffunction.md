---
title: beta_cdf () — обозреватель данных Azure
description: В этой статье описывается beta_cdf () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a98fe59755e47be8f4f4e53595d25bb260004236
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349233"
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

Если альфа ≤ 0 или бета ≤ 0, beta_cdf () возвращает значение NaN.

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
|0.9|10|20|Допустимые входные данные|0.999999999999959|
|1.5|10|20|x > 1, возвращает NaN|Не число|
|–10|10|20|x < 0, возвращает NaN|Не число|
|0.1|-1|20|Альфа-< 0, возвращает NaN|Не число|


**См. также:**


* Вычисление обратной функции бета-интегральной плотности см. в статье [Beta – Inv ()](./beta-invfunction.md).
* Сведения о плотности вероятности вычислений см. в разделе [Beta-PDF ()](./beta-pdffunction.md).