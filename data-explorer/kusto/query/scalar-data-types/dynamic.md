---
title: Динамические типы данных в Azure Data Explorer | Документация Майкрософт
description: В этой статье описываются динамические типы данных в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 2ef75f2249466a9796fb0313186546bd9088ff07
ms.sourcegitcommit: 28f18c3500992fd7dcfd95dffe2bae0161a22ca3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2020
ms.locfileid: "97792937"
---
# <a name="the-dynamic-data-type"></a>Динамический тип данных

Скалярный тип данных `dynamic` уникален тем, что может принимать любые значения других скалярных типов данных из приведенного ниже списка, а также массивов и контейнеров свойств. В частности, `dynamic` может принимать следующие значения:

* NULL.
* Значение любого из примитивных скалярных типов данных: `bool`, `datetime`, `guid`, `int`, `long`, `real`, `string` и `timespan`.
* Массив значений `dynamic`, содержащих ноль или более значений с индексацией с нуля.
* Контейнер свойств, который сопоставляет уникальные значения `string` со значениями `dynamic`.
  Контейнер свойств может не содержать или содержать сопоставления (называемые "слотами"), индексируемые по уникальным значениям `string`. Слоты при этом не упорядочены.

> [!NOTE]
> * Значения типа `dynamic` ограничены размером 1 МБ (2^20).
> * Хотя тип `dynamic` похож на JSON, он может содержать значения, которые не представлены в модели JSON, поскольку они не существуют в JSON (например, `long`, `real`, `datetime`, `timespan` и `guid`).
>   Поэтому при сериализации значений `dynamic` в представление JSON те значения, которые не могут быть представлены JSON, сериализуются в значения `string`. И наоборот, Kusto будет анализировать строки как строго типизированные значения, если они могут быть проанализированы таким образом.
>   Это касается типов `datetime`, `real`, `long` и `guid`. 
>   Дополнительные сведения об объектной модели JSON см. на сайте [json.org](https://json.org/).
> * Kusto не пытается сохранить порядок сопоставлений имен и значений в контейнере свойств, поэтому не следует предполагать, что порядок сохраняется. Для двух контейнеров свойств с одинаковым набором сопоставлений можно получить разные результаты, если они представлены, например, в виде значений `string`.

## <a name="dynamic-literals"></a>Динамические литералы

Литерал типа `dynamic` выглядит следующим образом:

`dynamic(` *значение* `)`

*Значение* может быть следующим:

* `null` — в этом случае литерал представляет динамическое значение NULL `dynamic(null)`.
* Другой литерал со скалярным типом данных — в этом случае литерал представляет литерал `dynamic` типа inner. Например, `dynamic(4)` является динамическим значением, хранящим значение 4 скалярного типа данных long.
* Массив динамических или других литералов: `[` *ListOfValues* `]`. Например, `dynamic([1, 2, "hello"])` является динамическим массивом из трех элементов: двух значений `long` и одного значения `string`.
* Контейнер свойств: `{` *имя* `=` *значение* ... `}`. Например, `dynamic({"a":1, "b":{"a":2}})` является контейнером свойств с двумя слотами, `a` и `b`, при этом второй слот является еще одним контейнером свойств.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

Для удобства литералы `dynamic`, отображаемые в самом тексте запроса, могут также включать другие литералы Kusto с типами: `datetime`, `timespan`, `real`, `long`, `guid`, `bool` и `dynamic`.
Это расширение для JSON недоступно при синтаксическом анализе строк (например, при использовании функции `parse_json` или при приеме данных), но позволяет выполнять следующие действия:

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

Чтобы преобразовать значение `string`, соответствующее правилам кодировки JSON, в значение `dynamic`, используйте функцию `parse_json`. Пример:

* `parse_json('[43, 21, 65]')` — массив чисел;
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` — словарь.
* `parse_json('21')` — одно значение динамического типа, содержащее число;
* `parse_json('"21"')` — одно значение динамического типа, содержащее строку.
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` — дает то же значение, что и `o` в приведенном выше примере.

> [!NOTE]
> В отличие от JavaScript, JSON требует заключать строки и имена свойств контейнера свойств в двойные кавычки (`"`).
> Поэтому обычно проще заключать закодированные JSON строковые литералы в одинарные кавычки (`'`).
  
В следующем примере показано, как можно определить таблицу, содержащую столбец `dynamic` (а также столбец `datetime`), а затем принять в нее одну запись. Здесь также показано, как кодировать строки JSON в CSV-файлах:

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would be:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allowed before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents.
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|Отметка времени                   | Трассировка                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## <a name="dynamic-object-accessors"></a>Методы доступа к динамическим объектам

Чтобы создать нижний индекс словаря, используйте точечную нотацию (`dict.key`) или скобочную нотацию (`dict["key"]`).
Если нижний индекс является строковой константой, оба параметра являются эквивалентными.

> [!NOTE] 
> Чтобы использовать выражение в качестве нижнего индекса, используйте скобочную нотацию. Если вы используете арифметические выражения, выражение внутри квадратных скобок должно быть заключено в круглые скобки.

В приведенных ниже примерах `dict` и `arr` являются столбцами динамического типа:

|Expression                        | Тип выражения метода доступа | Значение                                                                              | Комментарии                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | Имя сущности (столбец)     | Создает нижний индекс словаря с использованием значений столбца `col` в качестве ключа.              | Столбец должен иметь тип string.                 | 
|arr[index]                        | Индекс сущности (столбец)    | Создает нижний индекс массива с использованием значений столбца `index` в качестве индекса.              | Столбец должен иметь тип integer или boolean.     | 
|arr[-index]                       | Индекс сущности (столбец)    | Извлекает значение 'index'-th из конца массива.                             | Столбец должен иметь тип integer или boolean.     |
|arr[(-1)]                         | Индекс сущности             | Извлекает последнее значение в массиве                                                |                                               |
|arr[toint(indexAsString)]         | Вызов функции            | Приводит значения столбца `indexAsString` к типу int и использует их для создания нижнего индекса массива. |                                               |
|dict[['where']]                   | Ключевое слово, используемое в качестве имени сущности (столбец). | Создает нижний индекс словаря с использованием значений столбца `where` в качестве ключа.    | Имена сущностей, идентичные некоторым ключевым словам языка запросов, должны быть заключены в кавычки. | 
|dict.['where'] или dict['where']   | Константа                 | Создает нижний индекс словаря с использованием строки `where` в качестве ключа.                              |                                               |

**Совет для повышения производительности:** при возможности используйте нижние индексы констант.

При доступе к подчиненному объекту значения `dynamic` выдается другое значение `dynamic`, даже если подчиненный объект имеет другой базовый тип. Используйте функцию `gettype`, чтобы найти фактический базовый тип значения, и любую указанную ниже функцию приведения, чтобы привести его к фактическому типу.

## <a name="casting-dynamic-objects"></a>Приведение динамических объектов

> После создания нижнего индекса динамического объекта необходимо привести значение к простому типу.

|Expression | Значение | Тип|
|---|---|---|
| X | parse_json('[100,101,102]')| массиве|
|X[0]|parse_json('100')|Динамический|
|toint(X[1])|101| INT|
| Да | parse_json('{"a1":100, "a b c":"2015-01-01"}')| словарь|
|Y.a1|parse_json('100')|Динамический|
|Y["a b c"]| parse_json("2015-01-01")|Динамический|
|todate(Y["a b c"])|datetime(2015-01-01)| DATETIME|

Функции приведения:

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>Создание динамических объектов

Существует несколько функций, которые позволяют создавать новые объекты `dynamic`:

* [pack()](../packfunction.md) создает контейнер свойств из пар "имя-значение".
* [pack_array()](../packarrayfunction.md) создает массив из пар "имя-значение".
* [range()](../rangefunction.md) создает массив с арифметическими последовательностями чисел.
* [zip()](../zipfunction.md) объединяет "параллельные" значения из двух массивов в один массив.
* [repeat()](../repeatfunction.md) создает массив с повторяющимся значением.

Кроме того, существует несколько функций агрегирования, которые создают массивы `dynamic` для хранения статистических значений:

* [buildschema()](../buildschema-aggfunction.md) возвращает статистическую схему для нескольких значений `dynamic`.
* [make_bag()](../make-bag-aggfunction.md) возвращает контейнер свойств динамических значений в группе.
* [make_bag_if()](../make-bag-if-aggfunction.md) возвращает контейнер свойств динамических значений в группе (с предикатом).
* [make_list()](../makelist-aggfunction.md) возвращает массив, содержащий все значения, в последовательности.
* [make_list_if()](../makelistif-aggfunction.md) возвращает массив, содержащий все значения, в последовательности (с предикатом).
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) возвращает массив, содержащий все значения, в последовательности (в том числе значения NULL).
* [make_set()](../makeset-aggfunction.md) возвращает массив, содержащий все уникальные значения.
* [make_set_if()](../makesetif-aggfunction.md) возвращает массив, содержащий все уникальные значения (с предикатом).

## <a name="operators-and-functions-over-dynamic-types"></a>Операторы и функции для динамических типов

Полный список скалярных функций для динамических типов и массивов см. в [этом разделе](../scalarfunctions.md#dynamicarray-functions).

|Оператор или функция|Использование с динамическими типами данных|
|---|---|
| *значение* `in` *массив*| Возвращает значение true, если в массиве *array* есть элемент, который равен значению *value*.<br/>`where City in ('London', 'Paris', 'Rome')`
| *значение* `!in` *массив*| Возвращает значение true, если в массиве *array* нет ни одного элемента, который равен значению *value*.
|[`array_length(`array`)`](../arraylengthfunction.md)| Возвращает NULL, если это не массив
|[`bag_keys(`контейнер`)`](../bagkeysfunction.md)| Перечисляет все корневые ключи в динамическом объекте контейнера свойств.
|[`bag_merge(`bag1,...,bagN`)`](../bag-merge-function.md)| Объединяет динамические контейнеры свойств в динамический контейнер свойств со всеми объединенными свойствами.
|[`extractjson(`путь,объект`)`](../extractjsonfunction.md)|Использует путь path для перехода к объекту object.
|[`parse_json(`source`)`](../parsejsonfunction.md)| Преобразует строку формата JSON в динамический объект.
|[`range(`начало,конец,шаг`)`](../rangefunction.md)| Массив значений
|[`mv-expand` listColumn](../mvexpandoperator.md) | Реплицирует строку для каждого значения в списке в указанной ячейке.
|[`summarize buildschema(`столбец`)`](../buildschema-aggfunction.md) |Определяет схему типа по содержимому столбца.
|[`summarize make_bag(`столбец`)`](../make-bag-aggfunction.md) | Объединяет значения контейнера свойств (словарь) в столбце в один контейнер свойств без дублирования ключа.
|[`summarize make_bag_if(`столбец,предикат`)`](../make-bag-if-aggfunction.md) | Объединяет значения контейнера свойств (словарь) в столбце в один контейнер свойств без дублирования ключа (с предикатом).
|[`summarize make_list(`столбец`)` ](../makelist-aggfunction.md)| Выполняет сведение групп строк и помещает значения столбца в массив.
|[`summarize make_list_if(`столбец,предикат`)` ](../makelistif-aggfunction.md)| Выполняет сведение строк и помещает значения столбца в массив (с предикатом).
|[`summarize make_list_with_nulls(`столбец`)` ](../make-list-with-nulls-aggfunction.md)| Выполняет сведение строк и помещает значения столбца в массив (в том числе значения NULL).
|[`summarize make_set(`столбец`)`](../makeset-aggfunction.md) | Выполняет сведение групп строк и помещает значения столбца в массив без дублирования значений.
