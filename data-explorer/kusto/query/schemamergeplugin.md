---
title: подключаемый модуль schema_merge — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль schema_merge в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 2873f3d010464b82ef8cb6a9a3e09f7b0a56b8d9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345714"
---
# <a name="schema_merge-plugin"></a>Подключаемый модуль schema_merge

Объединяет определения табличной схемы в единую схему. 

Определения схемы должны быть в формате, созданном [`getschema`](./getschemaoperator.md) оператором.

`schema merge`Операция соединяет столбцы во входных схемах и пытается сократить типы данных до общих. Если типы данных не могут быть сокращены, на проблемном столбце отображается ошибка.

```kusto
let Schema1=Table1 | getschema;
let Schema2=Table2 | getschema;
union Schema1, Schema2 | evaluate schema_merge()
```

## <a name="syntax"></a>Синтаксис

`T``|` `evaluate` `schema_merge(` *Пресервеордер*`)`

## <a name="arguments"></a>Аргументы

* *Пресервеордер*(необязательный). Если задано значение `true` , направляет подключаемый модуль для проверки порядка столбцов в соответствии с определением первой хранимой табличной схемы. Если один и тот же столбец находится в нескольких схемах, порядковый номер столбца должен быть похож на порядковый номер первой схемы, в которой он появился. Значение по умолчанию — `true`.

## <a name="returns"></a>Результаты

`schema_merge`Подключаемый модуль возвращает выходные данные, аналогичные [`getschema`](./getschemaoperator.md) возвращаемым оператором.

## <a name="examples"></a>Примеры

Выполнить слияние со схемой, к которой добавлен новый столбец.

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

Слияние с схемой, которая имеет другой порядок столбцов ( `HttpStatus` порядковый номер изменяется с `1` на `2` в новом варианте).

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
|HttpStatus|-1|Ошибка (неизвестный тип CSL: ошибка (неупорядоченные столбцы))|Ошибка (неупорядоченные столбцы)|

Выполнить слияние с схемой, которая имеет другой порядок столбцов, но с параметром `PreserveOrder` `false` .

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
