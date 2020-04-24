---
title: series_fill_backward() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_fill_backward () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b8c3bb09c7067112fd358c94fd46ca85bea31358
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508744"
---
# <a name="series_fill_backward"></a>series_fill_backward()

Выполняет обратный заполнения интерполяции недостающих значений в серии.

Принимает выражение, содержащее динамический числовой массив, в качестве входная, заменяет все экземпляры missing_value_placeholder на ближайшее значение с правой стороны, кроме missing_value_placeholder и возвращает полученный массив. Сохранились самые правильные экземпляры missing_value_placeholder.

**Синтаксис**

`series_fill_backward(`*x*`[, `*missing_value_placeholder*`])`
* Возвращает серию *x* со всеми экземплярами *missing_value_placeholder* заполнены назад.

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
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|Arr|fill_forward|
|---|---|
|111,null,36,41,null,null,16,61,33,null, null|(111,36,36,41,16, 16,16,61,33,null,null)|

  
Для завершения интерполяции вышеуказанного массива можно использовать [series_fill_forward](series-fill-forwardfunction.md) или [серию-заполняние.](series-fill-constfunction.md)