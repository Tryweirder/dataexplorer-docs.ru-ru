---
title: beta_pdf () — обозреватель данных Azure
description: В этой статье описывается beta_pdf () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ebd4cb0ab8a5bffec717f83892a3ea11b35f409
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227644"
---
# <a name="beta_pdf"></a>beta_pdf()

Возвращает функцию бета-версии плотности вероятности.

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

Бета-распределение обычно используется для изучения вариативности в процентном отношении чего-либо в образцах, например часть дня, которую люди проводят перед телевизором.

**Синтаксис**

`beta_pdf(`*x* `, ` *альфа-канал* `, ` *бета-версия*`)`

**Аргументы**

* *x*: значение, для которого вычисляется функция.
* *альфа*— параметр распределения.
* *бета*— параметр распределения.

**Возвращает**

* [Функция плотности бета-версии](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function).

**Примечания**

Если любой из аргументов является нечисловым, beta_pdf () возвращает значение null.

Если x ≤ 0 или 1 ≤ x, beta_pdf () возвращает значение NaN.

Если альфа ≤ 0 или бета ≤ 0, beta_pdf () возвращает значение NaN.

**Примеры**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.5, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend r = beta_pdf(x, alpha, beta)
```

|x|alpha|beta|comment|r|
|---|---|---|---|---|
|0,5|10|20|Допустимые входные данные|0.746176019310951|
|1.5|10|20|x > 1, возвращает NaN|NaN|
|–10|10|20|x < 0, возвращает NaN|NaN|
|0,1|-1|20|Альфа-< 0, возвращает NaN|NaN|

**Ссылки**

* Вычисление обратной функции бета-интегральной плотности см. в статье [Beta – Inv ()](./beta-invfunction.md).
* Сведения о стандартной интегральной функции бета-распределения см. в статье [Beta-CDF ()](./beta-cdffunction.md).
