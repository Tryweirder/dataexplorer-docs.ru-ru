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
ms.openlocfilehash: 6c91275320a5ec404b6cd5fcbe8c84b4123bd2de
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349352"
---
# <a name="bag_unpack-plugin"></a>Подключаемый модуль bag_unpack

`bag_unpack`Подключаемый модуль распаковать один столбец типа `dynamic` , рассматривая каждый слот верхнего уровня каждого свойства как столбец.

    T | evaluate bag_unpack(col1)

## <a name="syntax"></a>Синтаксис

*T* `|` `evaluate` `bag_unpack(` *столбец* [ `,` *аутпутколумнпрефикс* ] [ `,` *колумнсконфликт* ] [ `,` *игноредпропертиес* ]`)`

## <a name="arguments"></a>Аргументы

* *T*: табличный ввод, *столбец* столбцов которого должен быть распакован.
* *Столбец*: столбец *T* для распаковки. Должен иметь тип `dynamic`.
* *Аутпутколумнпрефикс*: общий префикс, добавляемый ко всем столбцам, создаваемым подключаемым модулем. Этот аргумент является необязательным.
* *колумнсконфликт*: направление разрешения конфликтов столбцов. Этот аргумент является необязательным. При указании аргумента предполагается, что это строковый литерал, соответствующий одному из следующих значений:
    - `error`-Query выдает ошибку (по умолчанию)
    - `replace_source`-Исходный столбец заменен
    - `keep_source`-Исходный столбец хранится
* *игноредпропертиес*: необязательный набор свойств контейнера, которые следует игнорировать. При указании аргумента ожидается, что он будет константой `dynamic` массива с одним или несколькими строковыми литералами.

## <a name="returns"></a>Результаты

`bag_unpack`Подключаемый модуль возвращает таблицу с количеством записей в качестве табличного ввода (*T*). Схема таблицы совпадает со схемой табличного входа со следующими изменениями:

* Указанный входной столбец (*столбец*) удален.
* Схема расширяется с использованием любого количества столбцов, так как в значениях контейнера свойств верхнего уровня *T*есть разные слоты. Имя каждого столбца соответствует имени каждого слота, при необходимости с префиксом *аутпутколумнпрефикс*. Его тип является либо типом слота, если все значения одного и того же слота имеют одинаковый тип, либо `dynamic` значение, если значения различаются в типе.

**Примечания**

Выходная схема подключаемого модуля зависит от значений данных, делая ее "непредсказуемой" в качестве самих данных. Несколько выполнений подключаемого модуля, использующего различные входные данные, могут создать другую выходную схему.

Входные данные для подключаемого модуля должны быть таким образом, чтобы выходная схема состоять из всех правил табличной схемы. В частности:

* Имя выходного столбца не может совпадать с именем существующего столбца в табличном вводе *t*, если только это столбец не является распакованным (*столбец*), так как это приведет к созданию двух столбцов с одинаковым именем.

* Все имена слотов (при префиксе *аутпутколумнпрефикс*) должны быть допустимыми именами сущностей и следовать [правилам именования идентификаторов](./schema-entities/entity-names.md#identifier-naming-rules).

## <a name="examples"></a>Примеры

### <a name="expand-a-bag"></a>Развернуть контейнер


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

|Название  |Возраст|
|------|---|
|Джон  |20 |
|Данил  |40 |
|Jasmine|30 |


### <a name="expand-a-bag-with-outputcolumnprefix"></a>Расширение контейнера с помощью Аутпутколумнпрефикс

Разверните контейнер и используйте параметр, `OutputColumnPrefix` чтобы создать имена столбцов, которые начинаются с префикса "Property_".

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
|Джон|20|
|Данил|40|
|Jasmine|30|

### <a name="expand-a-bag-with-columnsconflict"></a>Расширение контейнера с помощью Колумнсконфликт

Разверните контейнер и используйте `columnsConflict` параметр для разрешения конфликтов между существующими столбцами и столбцами, созданными `bag_unpack()` оператором.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(Name:string, d:dynamic)
[
    'Old_name', dynamic({"Name": "John", "Age":20}),
    'Old_name', dynamic({"Name": "Dave", "Age":40}),
    'Old_name', dynamic({"Name": "Jasmine", "Age":30}),
]
| evaluate bag_unpack(d, columnsConflict='replace_source') // Use new name
```

|Название|Возраст|
|---|---|
|Джон|20|
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
| evaluate bag_unpack(d, columnsConflict='keep_source') // Keep old name
```

|Название|Возраст|
|---|---|
|Old_name|20|
|Old_name|40|
|Old_name|30|

### <a name="expand-a-bag-with-ignoredproperties"></a>Расширение контейнера с помощью Игноредпропертиес

Разверните контейнер и используйте параметр, `ignoredProperties` чтобы игнорировать определенные свойства в контейнере свойств.

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

|Название|
|---|
|Джон|
|Данил|
|Jasmine|
