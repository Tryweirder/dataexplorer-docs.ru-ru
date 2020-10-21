---
title: подключаемый модуль schema_merge — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль schema_merge в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 9a6f25a7ea1c75211d043fdbca7f99c16ff98e1b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250330"
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
