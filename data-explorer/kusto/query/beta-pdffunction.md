---
title: beta_pdf() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны beta_pdf () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53b86d88b05cc6c5cc31f1e3bbb9e3e712eed7f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517873"
---
# <a name="beta_pdf"></a>beta_pdf()

Возвращает бета-функцию плотности вероятности.

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

Бета-распределение обычно используется для изучения вариативности в процентном отношении чего-либо в образцах, например часть дня, которую люди проводят перед телевизором.

**Синтаксис**

`beta_pdf(`*x*`, `*альфа-бета*`, `*beta*`)`

**Аргументы**

* *x*: Значение, при котором можно оценить функцию.
* *альфа*: Параметр распределения.
* *бета*: Параметр распределения.

**Возвращает**

* [Функция вероятности бета-плотности.](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function)

**Примечания**

Если какой-либо аргумент ненумерен, beta_pdf() возвращает нулевую стоимость.

Если х 0 или 1 х, beta_pdf() возвращает значение NaN.

Если альфа-0 или бета-версия 0, beta_pdf() возвращает значение NaN.

**Примеры**

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
|0,5|10|20|Действительный вход|0.746176019310951|
|1.5|10|20|x > 1, дает NaN|NaN|
|–10|10|20|x < 0, дает NaN|NaN|
|0,1|-1|20|альфа < 0, дает NaN|NaN|

**Ссылки**

* Для вычисления обратной функции бета-кумулятивной плотности вероятности [см.](./beta-invfunction.md)
* Для стандартной функции кумулятивного [бета-распределения](./beta-cdffunction.md)см.