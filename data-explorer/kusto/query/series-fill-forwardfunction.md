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
ms.openlocfilehash: 7ea5210f0370b495c48615d28e763bf6e396d46e
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763708"
---
# <a name="series_fill_forward"></a>series_fill_forward()

Выполняет интерполяцию прямой заливки отсутствующих значений в ряде.

Входными данными является выражение, содержащее динамический числовой массив. Функция заменяет все экземпляры missing_value_placeholder ближайшим значением из левой части, отличной от missing_value_placeholder, и возвращает результирующий массив. Сохраняются крайние левые экземпляры missing_value_placeholder.

**Синтаксис**

`series_fill_forward(`*x* `[, ` *missing_value_placeholder*`])`
* Возвратит ряд *x* со всеми экземплярами *missing_value_placeholder* заполненные вперед.

**Аргументы**

* *x*: скалярное выражение с динамическим массивом, представляющее собой массив числовых значений. 
* *missing_value_placeholder*: необязательный параметр, который указывает заполнитель для замены отсутствующего значения. Значение по умолчанию — `double` (*null*).

**Примечания**

* Укажите значение *null* в качестве значения по умолчанию, чтобы применить функции интерполяции после [создания рядов](make-seriesoperator.md): 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* *Missing_value_placeholder* может иметь любой тип, который будет преобразован в фактические типы элементов. Оба значения `double` (*null*) `long` (*null*) и `int` (*null*) имеют одинаковое значение.
* Если missing_value_placeholder имеет значение (null) (или опущено, то есть имеет то же значение), результат может содержать значения *null* . Для заполнения этих значений *null* используйте другие функции интерполяции. В настоящее время только [series_outliers ()](series-outliersfunction.md) поддерживают значения *null* во входных массивах.
* Функции сохраняют исходный тип элементов массива.

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
