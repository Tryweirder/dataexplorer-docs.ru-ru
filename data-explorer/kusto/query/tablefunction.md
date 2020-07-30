---
title: Table () (Функция scope) — Azure обозреватель данных
description: В этой статье описывается таблица () (Функция scope) в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ba61537de8925b3eeaaffc8b5337e2ecfa343d88
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342399"
---
# <a name="table-scope-function"></a>Table () (Функция scope)

Функция Table () ссылается на таблицу, предоставляя ее имя в виде выражения типа `string` .

```kusto
table('StormEvent')
```

## <a name="syntax"></a>Синтаксис

`table``(` *TableName* [ `,` *область действия*]`)`

## <a name="arguments"></a>Аргументы

::: zone pivot="azuredataexplorer"

* *TableName*: выражение типа `string` , предоставляющее имя таблицы, на которую указывает ссылка. Значение этого выражения должно быть константой в точке вызова функции (т. е. он не может варьироваться в зависимости от контекста данных).

* *Область*данных. необязательный параметр типа `string` , который можно использовать для ограничения ссылки на таблицу на данные в соответствии с тем, как эти данные попадают в действующую [политику кэширования](../management/cachepolicy.md)таблицы. При использовании фактический аргумент должен быть константным `string` выражением с одним из следующих возможных значений:

    - `"hotcache"`: Будут использоваться ссылки только на данные, классифицированные как горячий кэш.
    - `"all"`: Будут использоваться ссылки на все данные в таблице.
    - `"default"`. Это то же самое `"all"` , что и, за исключением случаев, когда кластер был настроен для использования `"hotcache"` по умолчанию администратором кластера.

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*: выражение типа `string` , предоставляющее имя таблицы, на которую указывает ссылка. Значение этого выражения должно быть константой в точке вызова функции (т. е. он не может варьироваться в зависимости от контекста данных).

* *Область*данных. необязательный параметр типа `string` , который можно использовать для ограничения ссылки на таблицу на данные в соответствии с тем, как эти данные попадают в действующую политику кэширования таблицы. При использовании фактический аргумент должен быть константным `string` выражением с одним из следующих возможных значений:

    - `"hotcache"`: Будут использоваться ссылки только на данные, классифицированные как горячий кэш.
    - `"all"`: Будут использоваться ссылки на все данные в таблице.
    - `"default"`. Это то же самое, что `"all"` .

::: zone-end

## <a name="examples"></a>Примеры

### <a name="use-table-to-access-table-of-the-current-database"></a>Использование Table () для доступа к таблице текущей базы данных

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
table('StormEvent') | count
```

|Счетчик|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>Использование Table () внутри инструкций Let

Тот же запрос, который приведен выше, может быть переписан для использования встроенной функции (оператор Let), принимающей параметр, `tableName` который передается в функцию Table ().

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|Счетчик|
|---|
|59066|

### <a name="use-table-inside-functions"></a>Использование Table () в функциях

Тот же запрос, который приведен выше, можно переписывать для использования в функции, принимающей параметр, `tableName` который передается в функцию Table ().

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**Примечание.** такие функции можно использовать только локально, а не в запросах между кластерами.

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>Использование Table () с параметром, не являющимся константным

Параметр, который не является скалярной константной строкой, нельзя передать в качестве параметра `table()` функции.

Ниже приведен пример обходного решения для такого случая.

```kusto
let T1 = print x=1;
let T2 = print x=2;
let _choose = (_selector:string)
{
    union
    (T1 | where _selector == 'T1'),
    (T2 | where _selector == 'T2')
};
_choose('T2')

```

|x|
|---|
|2|
