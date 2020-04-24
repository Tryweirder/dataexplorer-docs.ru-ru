---
title: series_fill_forward() - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описывается series_fill_forward () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c79733aa1abf001f52eb07606c866904e370906
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508676"
---
# <a name="series_fill_forward"></a>series_fill_forward()

Выполняет передний залив интерполяции недостающих значений в серии.

Принимает выражение, содержащее динамический числовой массив, в качестве входная, заменяет все экземпляры missing_value_placeholder ближайшим значением с левой стороны, кроме missing_value_placeholder, и возвращает результирующую массив. Сохранились левые экземпляры missing_value_placeholder.

**Синтаксис**

`series_fill_forward(`*x*`[, `*missing_value_placeholder*`])`
* Будет возвращать серию *x* со всеми экземплярами *missing_value_placeholder* заполнены вперед.

**Аргументы**

* *x*: динамическое выражение масштабирования массива, представляющее собой массив числовых значений. 
* *missing_value_placeholder*: дополнительный параметр, который определяет заполнителя для недостающих значений, которые будут заменены. Значение по `double`умолчанию *(нулевой).*

**Примечания**

* Для применения любых функций интерполяции после [make-series](make-seriesoperator.md) рекомендуется указать *нулевую* в качестве значения по умолчанию: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *Missing_value_placeholder* может быть любого типа, который будет преобразован в фактические типы элементов. Поэтому `double`либо *(null),* `long`*(null)* или `int`*(null)* имеют то же значение.
* Если *missing_value_placeholder* missing_value_placeholder `double`*(null)*(или просто опущены, которые имеют то же значение), то результат может содержать *нулевые* значения. Используйте другие функции интерполяции для их заполнения. В настоящее время только [series_outliers ()](series-outliersfunction.md) поддерживают *нулевые* значения в вхотозах.
* Функции сохраняют оригинальный тип элементов массива.

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

|Arr|fill_forward|
|---|---|
|(null,null,36,41,null,null,16,61,33,null,null)|(null,null,36,41,41,41,16,61,33,33,33)|
   
Для завершения интерполяции вышеуказанного массива можно использовать [series_fill_backward](series-fill-backwardfunction.md) или [серию-заполняние.](series-fill-constfunction.md)