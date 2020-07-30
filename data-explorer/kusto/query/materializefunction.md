---
title: материализация () — Azure обозреватель данных
description: В этой статье описывается функция материализации () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: 0e08857e01ffa3da1bcb23d16d1df4908336f76f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346870"
---
# <a name="materialize"></a>materialize()

Позволяет кэшировать результат вложенного запроса во время выполнения запроса таким образом, чтобы другие вложенные запросы могли ссылаться на частичный результат.
 
## <a name="syntax"></a>Синтаксис

`materialize(`*expression*`)`

## <a name="arguments"></a>Аргументы

* *выражение*: табличное выражение, которое вычисляется и кэшируется во время выполнения запроса.

> [!NOTE]
> Материализация имеет ограничение размера кэша, равное **5 ГБ**. Это ограничение устанавливается для каждого узла кластера и является взаимным для всех запросов, выполняющихся одновременно. Если запрос использует `materialize()` и кэш не может вместить какие-либо данные, запрос будет прерван с ошибкой.

>[!TIP]
>
>* Отправьте все возможные операторы, которые уменьшают набор материализованных данных и сохраняют семантику запроса. Например, используйте фильтры или Project только обязательные столбцы.
>* Используйте материализации с Join или Union, если их операнды имеют взаимные вложенные запросы, которые можно выполнить один раз. Например, ветвление или объединение. См. [Пример использования оператора Join](#examples-of-query-performance-improvement).
>* Материализация может использоваться только в инструкциях let, если присвоить кэшированному результату имя. См. [Пример использования инструкций Let](#examples-of-using-materialize)).

## <a name="examples-of-query-performance-improvement"></a>Примеры повышения производительности запросов

В следующем примере показано `materialize()` , как можно использовать для повышения производительности запроса.
Выражение `_detailed_data` определяется с помощью `materialize()` функции и, следовательно, вычисляется только один раз.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|Состояние|EventType|евентперцентаже|События|
|---|---|---|---|
|ГАВАЙИ РАЗВЕДЫВАЕТЕ|ватерспаут|100|2|
|LAKE ОНТАРИО|Моря Сундерсторм Ветер|100|8|
|ЗАЛИВА АЛЯСКА|ватерспаут|100|4|
|АТЛАНТИЧЕСКОЕ ПОБЕРЕЖЬЕ|Моря Сундерсторм Ветер|95.2127659574468|179|
|LAKE ЕРИЕ|Моря Сундерсторм Ветер|92.5925925925926|25|
|ПО ТИХООКЕАНСКОМУ ВРЕМЕНИ|ватерспаут|90|9|
|LAKE МИЧИГАНА|Моря Сундерсторм Ветер|85.1648351648352|155|
|LAKE ХУРОН|Моря Сундерсторм Ветер|79.3650793650794|50|
|ЗАЛИВА МЕКСИКИ|Моря Сундерсторм Ветер|71.7504332755633|414|
|Гавайи|Высокая просматривайте|70.0218818380744|320|


В следующем примере создается набор случайных чисел и вычисляется: 
* количество различных значений в наборе ( `Dcount` )
* три верхних значения в наборе 
* сумма всех этих значений в наборе 
 
Эту операцию можно выполнить с помощью [пакетов](batches.md) и материализации:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

Результирующий набор 1:  

|DCount|
|---|
|2578351|

Результирующий набор 2: 

|value|
|---|
|9999998|
|9999998|
|9999997|

Результирующий набор 3: 

|Сумма|
|---|
|15002960543563|

## <a name="examples-of-using-materialize"></a>Примеры использования материализации ()

> [!TIP]
> Собросайте свой столбец во время приема, если большинство запросов извлекают поля из динамических объектов в миллионах строк.

Чтобы использовать `let` инструкцию со значением, которое используется более одного раза, используйте [функцию материализации ()](./materializefunction.md). Попробуйте отправить все возможные операторы, которые будут уменьшать материализованный набор данных и сохранить семантику запроса. Например, используйте фильтры или Project только обязательные столбцы.

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
```

Фильтр по `Text` является взаимным и может быть передан в материализовать выражение.
Запрос должен иметь только столбцы `Timestamp` , `Text` , `Resource1` и `Resource2` . Проецировать эти столбцы внутри материализованных выражений.
    
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
```
    
Если фильтры не идентичны, как в следующем запросе:  

```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
 ```

Если Объединенный фильтр значительно сокращает объем материализованных результатов, объедините оба фильтра в материализованный результат по логическому `or` выражению, как показано в запросе ниже. Однако следует сохранять фильтры в каждой части объединения, чтобы сохранить семантику запроса.
     
```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
```
    
