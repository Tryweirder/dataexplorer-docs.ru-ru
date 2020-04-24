---
title: schema_merge плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается schema_merge плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 67326a0e7a92d064613ee3a3de2851addb502fc9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509254"
---
# <a name="schema_merge-plugin"></a>schema_merge плагин

Слияние табликовых определений схемы в единую схему. 

Ожидается, что определения схембудут будут в формате, производимом оператором [getschema.](./getschemaoperator.md)

Столбики соединений операций schema, которые отображаются в схемах ввода и пытаются уменьшить типы данных до общего типа данных (в случае, если типы данных не могут быть уменьшены, ошибка отображается на проблемном столбце).

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

**Синтаксис**

`T``schema_merge(` *PreserveOrder* Заповедник `|` `evaluate``)`

**Аргументы**

* *PreserveOrder*: (Опционально) При установке `true`на, направляет на плагин для проверки этого порядка столбца, как это определено первой таббуларной схемой сохраняется. Другими словами, если один и тот же столбец отображается в нескольких схемах, то столбец должен быть таким, как в первой схеме, которая появилась. Значение по умолчанию — `true`.

**Возвращает**

Плагин `schema_merge` возвращает выход simiar к тому, что [получает оператор](./getschemaoperator.md) возвращается.

**Примеры**

Слияние со схемой, которая имеет новый столбец добавлен:

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, HttpStatus:int, Referrer:string)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*Результат*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|URI|0|System.String|строка|
|HttpStatus|1|System.Int32|INT|
|Referrer|2|System.String|строка|

Слияние со схемой, которая имеет`HttpStatus` различные заказы столбцов (порядковые изменения от `1` к `2` в новом варианте):

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge()
```

*Результат*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|URI|0|System.String|строка|
|Referrer|1|System.String|строка|
|HttpStatus|-1|ERROR (неизвестный тип CSL:ERROR (столбцы вышли из строя))|ERROR (столбцы вышли из строя)|

Слияние со схемой, которая имеет различные `PreserveOrder` заказы `false` столбца, но с набором на этот раз:

```kusto
let schema1 = datatable(Uri:string, HttpStatus:int)[] | getschema;
let schema2 = datatable(Uri:string, Referrer:string, HttpStatus:int)[] | getschema;
union schema1, schema2 | evaluate schema_merge(PreserveOrder = false)
```

*Результат*

|ColumnName | ColumnOrdinal | DataType | ColumnType|
|---|---|---|---|
|URI|0|System.String|строка
|Referrer|1|System.String|строка
|HttpStatus|2|System.Int32|INT|