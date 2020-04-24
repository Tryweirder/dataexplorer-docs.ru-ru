---
title: beta_inv () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается beta_inv () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 20bdf8bfc01ef3ac6c6a12f6a43d87fd7b5c07e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517890"
---
# <a name="beta_inv"></a>beta_inv()

Возвращает обратную функцию бета-кумулятивной вероятности бета-плотности.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Если *вероятность* = `beta_cdf(`*х*,... `)`, `beta_inv(`то *вероятность*,... `)` *x*x  = . 

Бета-распределение можно использовать в планировании проекта для моделирования вероятного времени выполнения, учитывающего ожидаемое время завершения и вариативность.

**Синтаксис**

`beta_inv(`*вероятность*`, `*альфа-бета* *alpha*`, ``)`

**Аргументы**

* *вероятность*: Вероятность, связанная с дистрибутивом бета-версии.
* *альфа*: Параметр распределения.
* *бета*: Параметр распределения.

**Возвращает**

* Обратная функция бета-кумулятивной плотности вероятности [beta_cdf()](./beta-cdffunction.md)

**Примечания**

Если какой-либо аргумент ненумерен, beta_inv() возвращает нулевую стоимость.

Если альфа-0 или бета-версия 0, beta_inv() возвращает нулевую стоимость.

Если вероятность 0 или вероятность > 1, beta_inv() возвращает значение NaN.

Учитывая значение вероятности, beta_inv() ищет это значение x, такое, чтобы beta_cdf (x, альфа, бета) - вероятность.

**Примеры**

```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|beta|comment|b|
|---|---|---|---|---|
|0,1|10|20|Действительный вход|0.226415022388749|
|1.5|10|20|р > 1, дает нулевую||
|0,1|-1|20|альфа < 0, дает NaN|NaN|

**См. также:**

* Для вычисления кумулятивной [beta-cdf()](./beta-cdffunction.md)функции бета-дистрибуции см.
* Для вычисления функции [бета-плотности](./beta-pdffunction.md)вероятности см.