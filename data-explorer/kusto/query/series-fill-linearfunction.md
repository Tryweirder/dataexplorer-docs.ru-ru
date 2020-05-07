---
title: series_fill_linear () — обозреватель данных Azure
description: В этой статье описывается series_fill_linear () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4ef02ab79b0701b4af74744a94e0ff795eb8c26a
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737255"
---
# <a name="series_fill_linear"></a>series_fill_linear()

Линейная интерполяция отсутствующих значений в ряде.

Принимает выражение, содержащее динамический числовой массив в качестве входных данных, выполняет линейную интерполяцию для всех экземпляров missing_value_placeholder и возвращает результирующий массив. Если начало и конец массива содержат missing_value_placeholder, то он будет заменен ближайшим значением, отличным от missing_value_placeholder. Эта функция может быть отключена. Если весь массив состоит из missing_value_placeholder, массив будет заполнен constant_value или 0, если не указан.  

**Синтаксис**

`series_fill_linear(`*x*`[,` ` [,`*fill_edges*fill_edges` [,` *missing_value_placeholder*x*constant_value*`]]]))`
* Будет возвращать линейную интерполяцию по *оси x* с использованием указанных параметров.
 

**Аргументы**

* *x*: скалярное выражение с динамическим массивом, представляющее собой массив числовых значений.
* *missing_value_placeholder*: необязательный параметр, который указывает заполнитель для замены отсутствующих значений. Значение по умолчанию — `double`(*null*).
* *fill_edges*: логическое значение, указывающее, следует ли заменять *missing_value_placeholder* в начале и в конце массива на ближайшее значение. По умолчанию *имеет значение true* . Если задано значение *false*, то *missing_value_placeholder* в начале и в конце массива будут сохранены.
* *constant_value*: необязательный параметр, относящийся только к массивам, полностью состоит из значений *null* . Этот параметр задает постоянное значение для заполнения ряда. Значение по умолчанию — *0*. Установка этого параметра в `double`значение (*null*) фактически оставляет значения *null* там, где они есть.

**Примечания**

* Чтобы применить любые функции интерполяции после выполнения действия [Series](make-seriesoperator.md), укажите значение *null* в качестве значения по умолчанию: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *Missing_value_placeholder* может иметь любой тип, который будет преобразован в фактические типы элементов. Таким образом, оба `double`значения (*null*) `long`, (*null*) `int`или (*null*) имеют одинаковое значение.
* Если *missing_value_placeholder* имеет `double`*значение (NULL*) (или опущено, то есть имеет то же значение), результат может содержать значения *null* . Используйте другие функции интерполяции для заполнения этих значений *null* . В настоящее время только [series_outliers ()](series-outliersfunction.md) поддерживают значения *null* во входных массивах.
* Функция сохраняет исходный тип элементов массива. Если x содержит только int или long, линейная интерполяция будет возвращать округленные интерполяции значений, а не точные.

**Пример**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null, 111.0, null, 36.0, 41.0, null, null, 16.0, 61.0, 33.0, null, null]), // Array of double    
    dynamic([null, 111,   null, 36,   41,   null, null, 16,   61,   33,   null, null]), // Similar array of int
    dynamic([null, null, null, null])                                                   // Array with missing values only
];
data
| project arr, 
          without_args = series_fill_linear(arr),
          with_edges = series_fill_linear(arr, double(null), true),
          wo_edges = series_fill_linear(arr, double(null), false),
          with_const = series_fill_linear(arr, double(null), true, 3.14159)  

```

|`arr`|`without_args`|`with_edges`|`wo_edges`|`with_const`|
|---|---|---|---|---|
|[null, 111.0, null, 36.0, 41.0, null, null, область невидимости, 61.0, 33.0, null, NULL]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, область видимости, 61.0, 33.0, 33.0, 33.0]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, область видимости, 61.0, 33.0, 33.0, 33.0]|[null, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, область невидимости, 61.0, 33.0, null, NULL]|[111.0, 111.0, 73.5, 36.0, 41.0, 32.667, 24.333, область видимости, 61.0, 33.0, 33.0, 33.0]|
|[null, 111, null, 36, 41, null, null, 16, 61, 33, null, NULL]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[111111, 73, 36, 41, 32, 24, 16, 61, 33, 33, 33]|[null, 111, 73, 36, 41, 32, 24, 16, 61, 33, null, NULL]|[111111, 74, 38, 41, 32, 24, 16, 61, 33, 33, 33]|
|[null, null, null, NULL]|[0.0, 0.0, 0.0, 0.0]|[0.0, 0.0, 0.0, 0.0]|[0.0, 0.0, 0.0, 0.0]|[3,14159, 3,14159, 3,14159, 3,14159]|