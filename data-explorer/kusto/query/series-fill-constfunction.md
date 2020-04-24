---
title: series_fill_const () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_fill_const () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7f5f939068737bdd6519fc0c63b663d19ae076a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508778"
---
# <a name="series_fill_const"></a>series_fill_const()

Заменяет недостающие значения в серии на указанное постоянное значение.

Принимает выражение, содержащее динамический числовой массив, в качестве вхотворного, заменяет все экземпляры missing_value_placeholder указанными constant_value и возвращает результирующую массив.

**Синтаксис**

`series_fill_const(`*x*`[, `*constant_value* `[,` *missing_value_placeholder*`]])`
* Будет возвращаться серии *x* со всеми экземплярами *missing_value_placeholder* заменены *на constant_value*.

**Аргументы**

* *x*: динамическое выражение масштабирования массива, представляющее собой массив числовых значений.
* *constant_value*: параметр, который определяет заполнителя для недостающих значений, которые будут заменены. Значение по умолчанию *0*. 
* *missing_value_placeholder*: дополнительный параметр, который определяет заполнителя для недостающих значений, которые будут заменены. Значение по `double`умолчанию *(нулевой).*

**Примечания**
* Можно создать серию с постоянным заполнением `default = ` одного вызова с помощью синтаксиса *DefaultValue* (или просто опуская, который будет принимать 0). Дополнительную информацию можно узнать из [серии make-series.](make-seriesoperator.md)

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* Для применения любых функций интерполяции после [make-series](make-seriesoperator.md) рекомендуется указать *нулевую* в качестве значения по умолчанию: 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* *Missing_value_placeholder* может быть любого типа, который будет преобразован в фактические типы элементов. Поэтому `double`либо *(null),* `long`*(null)* или `int`*(null)* имеют то же значение.
* Функция сохраняет оригинальный тип элементов массива. 

**Пример**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|Arr|fill_const1|fill_const2|
|---|---|---|
|111,null,36,41,23,null,16,61,33,null,null, null|[111,0.0,36,41,23,0.0,16,61,33,0.0,0.0]|[111,-1,36,41,23,-1,16,61,33,-1,-1]|