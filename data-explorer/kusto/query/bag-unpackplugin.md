---
title: подключаемый модуль bag_unpack — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль bag_unpack в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 1823a9d875c6294f360fbce77fcb5e1d2c968019
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818558"
---
# <a name="bag_unpack-plugin"></a>подключаемый модуль bag_unpack

`bag_unpack`Подключаемый модуль распаковать один столбец типа `dynamic` , рассматривая каждый слот верхнего уровня каждого свойства как столбец.

    T | evaluate bag_unpack(col1)

**Синтаксис**

*T* `|` `evaluate` `bag_unpack(` *столбец* [ `,` *аутпутколумнпрефикс* ] [ `,` *колумнсконликт* ] [ `,` *игноредпропертиес* ]`)`

**Аргументы**

* *T*: табличный ввод, *столбец* столбцов которого должен быть распакован.
* *Столбец*: столбец *T* для распаковки. Должен иметь тип `dynamic`.
* *Аутпутколумнпрефикс*: общий префикс, добавляемый ко всем столбцам, создаваемым подключаемым модулем. Этот аргумент является необязательным.
* *колумнсконликт*: направление разрешения конфликтов столбцов. Этот аргумент является необязательным. При указании аргумента предполагается, что это строковый литерал, соответствующий одному из следующих значений:
    - `error`-Query выдает ошибку (по умолчанию)
    - `replace_source`-Исходный столбец заменен
    - `keep_source`-Исходный столбец хранится
* *игноредпропертиес*: необязательный набор свойств контейнера, которые следует игнорировать. При указании аргумента ожидается, что он будет константой `dynamic` массива с одним или несколькими строковыми литералами.

**Возвращает**

`bag_unpack`Подключаемый модуль возвращает таблицу с количеством записей в качестве табличного ввода (*T*). Схема таблицы совпадает со схемой табличного входа со следующими изменениями:

* Указанный входной столбец (*столбец*) удален.

* Схема расширяется с использованием любого количества столбцов, так как в значениях контейнера свойств верхнего уровня *T*есть разные слоты. Имя каждого столбца соответствует имени каждого слота, при необходимости с префиксом *аутпутколумнпрефикс*. Его тип является либо типом слота (если все значения одного и того же слота имеют одинаковый тип), либо `dynamic` (если значения различаются в типе).

**Примечания**

Выходная схема подключаемого модуля зависит от значений данных, делая ее "непредсказуемой" в качестве самих данных. Таким образом, несколько выполнений подключаемого модуля, использующего различные входные данные, могут создать другую выходную схему.

Входные данные для подключаемого модуля должны быть такими, что выходная схема соответствует всем правилам табличной схемы. В частности:

1. Имя выходного столбца не может совпадать с именем существующего столбца в табличном вводе *T* , если это не столбец, который должен быть распакован (*столбец*), так как это приведет к созданию двух столбцов с одинаковым именем.

2. Все имена слотов (при префиксе *аутпутколумнпрефикс*) должны быть допустимыми именами сущностей и следовать [правилам именования идентификаторов](./schema-entities/entity-names.md#identifier-naming-rules).

**Примеры**

Расширение контейнера:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d)
```

|Имя  |Возраст|
|------|---|
|Виталий  |20 |
|Данил  |40 |
|Jasmine|30 |

Расширение контейнера и использование `OutputColumnPrefix` параметра для создания имен столбцов, начинающихся с префикса "Property_":

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20}),
    dynamic({"Name": "Dave", "Age":40}),
    dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, 'Property_')
```

|Property_Name|Property_Age|
|---|---|
|Виталий|20|
|Данил|40|
|Jasmine|30|

Расширение контейнера и использование `columnsConlict` параметра для разрешения конфликтов между существующими столбцами и столбцами, созданными `bag_unpack()` оператором.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConlict='replace_source') // Use new name
```

|Имя|Возраст|
|---|---|
|Виталий|20|
|Данил|40|
|Jasmine|30|

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConlict='keep_source') // Keep old name
```

|Имя|Возраст|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

Расширение контейнера и использование `ignoredProperties` параметра для пропуска некоторых свойств, существующих в контейнере свойств.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(d:dynamic)
[
    dynamic({"Name": "John", "Age":20, "Address": "Address-1" }),
    dynamic({"Name": "Dave", "Age":40, "Address": "Address-2"}),
    dynamic({"Name": "Jasmine", "Age":30, "Address": "Address-3"}),
]
// Ignore 'Age' and 'Address' properties
| evaluate bag_unpack(d, ignoredProperties=dynamic(['Address', 'Age']))
```

|Имя|
|---|
|Виталий|
|Данил|
|Jasmine|
