---
title: series_fill_const () — обозреватель данных Azure
description: В этой статье описывается series_fill_const () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e078919af16a9d2f7dadba0a309932b3a39b6ced
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763244"
---
# <a name="series_fill_const"></a>series_fill_const()

Заменяет отсутствующие значения в ряде с указанным постоянным значением.

Принимает выражение, содержащее динамический числовой массив в качестве входных данных, заменяет все экземпляры missing_value_placeholder заданными constant_value и возвращает результирующий массив.

**Синтаксис**

`series_fill_const(`*x* `[, ` *constant_value* `[,` *missing_value_placeholder*`]])`
* Возвратит ряд *x* со всеми экземплярами *missing_value_placeholder* заменяются *constant_value*.

**Аргументы**

* *x*: скалярное выражение динамического массива, которое является массивом числовых значений.
* *constant_value*: параметр, указывающий заполнитель для замены отсутствующего значения. Значение по умолчанию — *0*. 
* *missing_value_placeholder*: необязательный параметр, указывающий заполнитель для замены отсутствующего значения. Значение по умолчанию — `double` (*null*).

**Примечания**
* Можно создать ряд, который заполняется константным значением, с помощью `default = ` синтаксиса *DefaultValue* (или просто пропуская, что будет считать 0). Дополнительные сведения см. в разделе [make-Series](make-seriesoperator.md).

```kusto
make-series num=count() default=-1 on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* Чтобы применить любые функции интерполяции после [make-Series](make-seriesoperator.md), укажите значение *null* в качестве значения по умолчанию: 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* *Missing_value_placeholder* может иметь любой тип, который будет преобразован в фактические типы элементов. Таким образом, оба значения `double` (*null*), `long` (*null*) или `int` (*null*) имеют одинаковое значение.
* Функция сохраняет исходный тип элементов массива. 

**Пример**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(`arr`: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|`arr`|`fill_const1`|`fill_const2`|
|---|---|---|
|[111, null, 36, 41, 23, null, 16, 61, 33, null, NULL]|[111, 0.0, 36, 41, 23, 0.0, 16, 61, 33, 0.0, 0.0]|[111,-1, 36, 41, 23,-1, 16, 61, 33,-1,-1]|
