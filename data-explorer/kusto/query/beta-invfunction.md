---
title: beta_inv () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается beta_inv () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e302f997addbdc4f2f9da4bd906844dbd1d07a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247998"
---
# <a name="beta_inv"></a>beta_inv()

Возвращает обратное значение бета-версии интегральной функции плотности Beta.

```kusto
beta_inv(0.1, 10.0, 50.0)
```

Если *вероятность*  =  `beta_cdf(` *x*,... `)` , то `beta_inv(` *вероятность*,... `)`  =  *x*. 

Бета-распределение можно использовать в планировании проекта для моделирования вероятного времени выполнения, учитывающего ожидаемое время завершения и вариативность.

## <a name="syntax"></a>Синтаксис

`beta_inv(`*вероятность* `, ` *альфа-канал* `, ` *бета-версия*`)`

## <a name="arguments"></a>Аргументы

* *вероятность*: вероятность, связанная с бета-распределением.
* *альфа*— параметр распределения.
* *бета*— параметр распределения.

## <a name="returns"></a>Результаты

* Обратная функция бета-версии интегральной функции плотности вероятности [beta_cdf ()](./beta-cdffunction.md)

**Примечания**

Если любой из аргументов является нечисловым, beta_inv () возвращает значение null.

Если альфа ≤ 0 или бета ≤ 0, beta_inv () возвращает значение null.

Если вероятность ≤ 0 или вероятность > 1, beta_inv () возвращает значение NaN.

Учитывая значение вероятности, beta_inv () ищет значение x таким, чтобы beta_cdf (x, Alpha, бета-версия) = вероятность.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|бета-версия|comment|b|
|---|---|---|---|---|
|0,1|10|20|Допустимые входные данные|0.226415022388749|
|1.5|10|20|p > 1, дает значение null||
|0,1|-1|20|Альфа-< 0, возвращает NaN|не число|

## <a name="see-also"></a>См. также раздел

* Вычисление интегральной функции бета-распределения см. в статье [Beta-CDF ()](./beta-cdffunction.md).
* Сведения о плотности бета-версии с вероятностью вычислений см. в разделе [Beta PDF ()](./beta-pdffunction.md).
