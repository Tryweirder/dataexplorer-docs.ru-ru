---
title: series_fill_backward () — обозреватель данных Azure
description: В этой статье описывается series_fill_backward () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 211ac6cb078e2f61243f4616f9141a6f4834d464
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741804"
---
# <a name="series_fill_backward"></a>series_fill_backward()

Осуществляет обратную заливку отсутствующих значений в ряде.

Входными данными является выражение, содержащее динамический числовой массив. Функция заменяет все экземпляры missing_value_placeholder ближайшим значением из правой части (кроме missing_value_placeholder) и возвращает результирующий массив. Сохраняются самые правые экземпляры missing_value_placeholder.

**Синтаксис**

`series_fill_backward(`*x*`[, `*missing_value_placeholder* x`])`
* Возвратит ряд *x* со всеми экземплярами *missing_value_placeholder* заполните обратную сторону.

**Аргументы**

* *x*: скалярное выражение с динамическим массивом, представляющее собой массив числовых значений.
* *missing_value_placeholder*: Этот необязательный параметр указывает заполнитель для отсутствующих значений. Значение по умолчанию `double`— (*null*).

**Примечания**

* Укажите значение *null* в качестве значения по умолчанию, чтобы применить любые функции интерполяции после [создания рядов](make-seriesoperator.md): 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *Missing_value_placeholder* может иметь любой тип, который будет преобразован в фактические типы элементов. Оба `double`значения (*null*) `long`, (*null*) `int`и (*null*) имеют одинаковое значение.
* Если *missing_value_placeholder* имеет `double`*значение (NULL*) (или опущено, то есть имеет то же значение), результат может содержать значения *null* . Для заполнения этих значений *null* используйте другие функции интерполяции. В настоящее время только [series_outliers ()](series-outliersfunction.md) поддерживают значения *null* во входных массивах.
* Функция сохраняет исходный тип элементов массива.

**Пример**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|`arr`|`fill_forward`|
|---|---|
|[111, null, 36, 41, null, null, 16, 61, 33, null, NULL]|[111, 36, 36, 41, 16, 16, 16, 61, 33, null, NULL]|

  
Чтобы завершить интерполяцию приведенного выше массива, используйте [series_fill_forward](series-fill-forwardfunction.md) или " [Fill-FILLIN-const](series-fill-constfunction.md) ".
