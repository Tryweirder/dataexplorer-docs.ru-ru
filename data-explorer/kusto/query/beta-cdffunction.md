---
title: beta_cdf() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается beta_cdf () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4faaeddc647d047755108f3c993db855a56de363
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517924"
---
# <a name="beta_cdf"></a>beta_cdf()

Возвращает стандартную функцию кумулятивного бета-дистрибуции.

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

Если *вероятность* = `beta_cdf(`*х*,... `)`, `beta_inv(`то *вероятность*,... `)` *x*x  = .

Бета-распределение обычно используется для изучения вариативности в процентном отношении чего-либо в образцах, например часть дня, которую люди проводят перед телевизором.

**Синтаксис**

`beta_cdf(`*x*`, `*альфа-бета*`, `*beta*`)`

**Аргументы**

* *x*: Значение, при котором можно оценить функцию.
* *альфа*: Параметр распределения.
* *бета*: Параметр распределения.

**Возвращает**

* [Функция кумулятивного бета-распределения.](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function)

**Примечания**

Если какой-либо аргумент ненумерен, beta_cdf() возвращает нулевую стоимость.

Если x < 0 или x > 1, beta_cdf() возвращает значение NaN.

Если альфа-0 или бета-версия 0, beta_cdf() возвращает значение NaN.

**Примеры**

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

|x|alpha|beta|comment|b|
|---|---|---|---|---|
|0.9|10|20|Действительный вход|0.999999999999959|
|1.5|10|20|x > 1, дает NaN|NaN|
|–10|10|20|x < 0, дает NaN|NaN|
|0,1|-1|20|альфа < 0, дает NaN|NaN|


**См. также:**


* Для вычисления обратной функции бета-кумулятивной плотности вероятности [см.](./beta-invfunction.md)
* Для функции вычислительной вероятности, [см.](./beta-pdffunction.md)