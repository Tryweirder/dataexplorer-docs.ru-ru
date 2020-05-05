---
title: series_fill_forward () — обозреватель данных Azure
description: В этой статье описывается series_fill_forward () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cdf9b84f684a2a4dfdb508f1ac5762039da8275d
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741710"
---
# <a name="series_fill_forward"></a>series_fill_forward()

Выполняет интерполяцию прямой заливки отсутствующих значений в ряде.

Входными данными является выражение, содержащее динамический числовой массив. Функция заменяет все экземпляры missing_value_placeholder ближайшим значением из левой части, отличной от missing_value_placeholder, и возвращает результирующий массив. Сохраняются крайние левые экземпляры missing_value_placeholder.

**Синтаксис**

`series_fill_forward(`*x*`[, `*missing_value_placeholder* x`])`
* Возвратит ряд *x* со всеми экземплярами *missing_value_placeholder* заполненные вперед.

**Аргументы**

* *x*: скалярное выражение с динамическим массивом, представляющее собой массив числовых значений. 
* *missing_value_placeholder*: необязательный параметр, который указывает заполнитель для замены отсутствующего значения. Значение по умолчанию — `double`(*null*).

**Примечания**

* Укажите значение *null* в качестве значения по умолчанию, чтобы применить функции интерполяции после [создания рядов](make-seriesoperator.md): 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *Missing_value_placeholder* может иметь любой тип, который будет преобразован в фактические типы элементов. Оба `double`значения*null*(NULL `long`) (*null*) `int`и (*null*) имеют одинаковое значение.
* Если missing_value_placeholder имеет значение (null) (или опущено, то есть имеет то же значение), результат может содержать значения *null* . Для заполнения этих значений *null* используйте другие функции интерполяции. В настоящее время только [series_outliers ()](series-outliersfunction.md) поддерживают значения *null* во входных массивах.
* Функции сохраняют исходный тип элементов массива.

**Пример**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|`arr`|`fill_forward`|
|---|---|
|[null, null, 36, 41, null, null, 16, 61, 33, null, NULL]|[null, null, 36, 41, 41, 41, 16, 61, 33, 33, 33]|
   
Чтобы завершить интерполяцию приведенного выше массива, используйте [series_fill_backward](series-fill-backwardfunction.md) или " [Fill-FILLIN-const](series-fill-constfunction.md) ".