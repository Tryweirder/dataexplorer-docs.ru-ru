---
title: array_sort_desc () — обозреватель данных Azure
description: В этой статье описывается array_sort_desc () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 09/22/2020
ms.openlocfilehash: 83be80a7eb440e73fd19f213839cef7d58ec1512
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169653"
---
# <a name="array_sort_desc"></a>array_sort_desc ()

Получает один или несколько массивов. Сортирует первый массив в убывающем порядке. Упорядочивает оставшиеся массивы в соответствии с переупорядоченным первым массивом.

## <a name="syntax"></a>Синтаксис

`array_sort_desc(`*массив1*[,..., *аргументн*]`)`

`array_sort_desc(`*массив1*[,..., *аргументн*] `,` *nulls_last*`)`

Если *nulls_last* не указан, используется значение по умолчанию `true` .

## <a name="arguments"></a>Аргументы

* *массив1... Аррайн*: входные массивы.
* *nulls_last*: логическое значение, указывающее, `null` должны ли быть последними

## <a name="returns"></a>Возвращаемое значение

Возвращает то же количество массивов, что и во входных данных, с первым массивом, отсортированным в возрастающем порядке, и оставшиеся массивы, упорядоченные в соответствии с переупорядоченным первым массивом.

`null` будет возвращаться для каждого массива, который отличается от длины от первого.

Если массив содержит элементы различных типов, он будет отсортирован в следующем порядке:

* Числовые `datetime` элементы, и `timespan`
* Строковые элементы
* Элементы GUID
* Все остальные элементы

## <a name="example-1---sorting-two-arrays"></a>Пример 1. Сортировка двух массивов

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let array1 = dynamic([1,3,4,5,2]);
let array2 = dynamic(["a","b","c","d","e"]);
print array_sort_desc(array1,array2)
```

|`array1_sorted`|`array2_sorted`|
|---|---|
|[5, 4, 3, 2, 1]|["d", "c", "b", "e", "a"]|

> [!Note]
> Имена выходных столбцов создаются автоматически на основе аргументов функции. Чтобы назначить различные имена выходным столбцам, используйте следующий синтаксис: `... | extend (out1, out2) = array_sort_desc(array1,array2)`

## <a name="example-2---sorting-substrings"></a>Пример 2. Сортировка подстрок

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Names = "John,Paul,George,Ringo";
let SortedNames = strcat_array(array_sort_desc(split(Names, ",")), ",");
print result = SortedNames
```

|`result`|
|---|
|Ринго, пол, Джон, Георгия|

## <a name="example-3---combining-summarize-and-array_sort_desc"></a>Пример 3. объединение сводки и array_sort_desc

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable(command:string, command_time:datetime, user_id:string)
[
    'chmod',   datetime(2019-07-15),   "user1",
    'ls',      datetime(2019-07-02),   "user1",
    'dir',     datetime(2019-07-22),   "user1",
    'mkdir',   datetime(2019-07-14),   "user1",
    'rm',      datetime(2019-07-27),   "user1",
    'pwd',     datetime(2019-07-25),   "user1",
    'rm',      datetime(2019-07-23),   "user2",
    'pwd',     datetime(2019-07-25),   "user2",
]
| summarize timestamps = make_list(command_time), commands = make_list(command) by user_id
| project user_id, commands_in_chronological_order = array_sort_desc(timestamps, commands)[1]
```

|`user_id`|`commands_in_chronological_order`|
|---|---|
|user1|[<br>  "RM",<br>  "pwd",<br>  "Dir",<br>  "chmod",<br>  "mkdir",<br>  Местоположение<br>]|
|user2|[<br>  "pwd",<br>  диспетчере<br>]|

> [!Note]
> Если данные могут содержать `null` значения, используйте [make_list_with_nulls](make-list-with-nulls-aggfunction.md) вместо [make_list](makelist-aggfunction.md).

## <a name="example-4---controlling-location-of-null-values"></a>Пример 4. Управление расположением `null` значений

По умолчанию `null` значения в отсортированном массиве помещаются последними. Однако вы можете явно управлять им, добавив `bool` значение в качестве последнего аргумента в `array_sort_desc()` .

Пример с поведением по умолчанию:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]))
```

|`print_0`|
|---|
|["желтый", "зеленый", "синий", null, NULL]|

Пример с поведением, не используемым по умолчанию:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print array_sort_desc(dynamic([null,"blue","yellow","green",null]), false)
```

|`print_0`|
|---|
|[null, null, "желтый", "зеленый", "синий"]|

## <a name="see-also"></a>См. также

Чтобы отсортировать первый массив в возрастающем порядке, используйте [array_sort_asc ()](arraysortascfunction.md).
