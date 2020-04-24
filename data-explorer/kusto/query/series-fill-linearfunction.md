---
title: series_fill_linear() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны series_fill_linear () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f9a12d172a1580d6a9e575b78b14404dce7820e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508659"
---
# <a name="series_fill_linear"></a>series_fill_linear()

Выполняет линейную интерполяцию недостающих значений в серии.

Принимает выражение, содержащее динамический числовой массив в качестве ввода, выполняет линейную интерполяцию для всех экземпляров missing_value_placeholder и возвращает полученный массив. Если начало и конец массива содержит missing_value_placeholder, то он будет заменен на ближайшее значение, не missing_value_placeholder (может быть выключен). Если весь массив состоит из missing_value_placeholder то массив будет заполнен constant_value или 0, если не указано.  

**Синтаксис**

`series_fill_linear(`*x* `[,` *missing_value_placeholder*` [,`*fill_edges*` [,`*constant_value*`]]]))`
* Возвращает серию линейной интерполяции *x* с использованием определенных параметров.
 

**Аргументы**

* *x*: динамическое выражение масштабирования массива, представляющее собой массив числовых значений.
* *missing_value_placeholder*: дополнительный параметр, который определяет заполнитель для "пропавших цен", которые будут заменены. Значение по `double`умолчанию *(нулевой).*
* *fill_edges*: значение Boolean, которое указывает, следует ли заменить *missing_value_placeholder* в начале и конце массива на ближайшее значение. *Правда* по умолчанию. Если установка *на ложное,* то *missing_value_placeholder* в начале и конце массива будет сохранена.
* *constant_value*: дополнительный параметр, относящихся только к массивам, полностью состоит из *нулевых* значений, которые определяют постоянное значение для заполнения серии. Значение по умолчанию *0*. Установка этого параметра его `double`*(null*) будет эффективно оставить *нулевые* значения, где они находятся.

**Примечания**

* Для применения любых функций интерполяции после [make-series](make-seriesoperator.md) рекомендуется указать *нулевую* в качестве значения по умолчанию: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *Missing_value_placeholder* может быть любого типа, который будет преобразован в фактические типы элементов. Поэтому `double`либо *(null),* `long`*(null)* или `int`*(null)* имеют то же значение.
* Если *missing_value_placeholder* missing_value_placeholder `double`*(null)*(или просто опущены, которые имеют то же значение), то результат может содержать *нулевые* значения. Используйте другие функции интерполяции для их заполнения. В настоящее время только [series_outliers ()](series-outliersfunction.md) поддерживают *нулевые* значения в вхотозах.
* Функция сохраняет оригинальный тип элементов массива. Если *x* содержит только *int* или *длинные* элементы, то линейная интерполяция вернет округлые интерполированные значения, а не точные.

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

|Arr|without_args|with_edges|wo_edges|with_const|
|---|---|---|---|---|
|(null,111.0,null,36.0,41.0,null,null,16.0,61.0,33.0,null, null)|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|(null,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,null,null, null)|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|
|(null,111,null,36,41,null,null,16,61,33,null,null)|[111,111,73,36,41,32,24,16,61,33,33,33]|[111,111,73,36,41,32,24,16,61,33,33,33]|(null,111,73,36,41,32,24,16,61,33,null,null)|[111,111,74,38, 41,32,24,16,61,33,33,33]|
|(недействительно, недействительно, недействительно)|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[3.14159,3.14159,3.14159,3.14159]|