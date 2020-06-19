---
title: Оператор Parse-WHERE — Azure обозреватель данных
description: В этой статье описывается оператор Parse-WHERE в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: 48231d24ca1e49938629dd9912804c5858d11ae1
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071883"
---
# <a name="parse-where-operator"></a>Оператор parse-where

Вычисляет строковое выражение и анализирует его значение в одном или нескольких вычисляемых столбцах. Результатом являются только успешно проанализированные строки. 

См. раздел [оператор Parse](parseoperator.md), который создает значения NULL для неудачно проанализированных строк.

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**Синтаксис**

*T* `| parse-where` [ `kind=regex` [ `flags=regex_flags` ] | `simple` ] *выражение* `with` `*` (*стрингконстант* *ColumnName* [ `:` *ColumnType*]) `*` ...

**Аргументы**

* *T*: входная таблица.

* *тип*: 

    * *Simple* (по умолчанию): стрингконстант — это регулярное строковое значение, а совпадение —. Все строковые разделители должны присутствовать в проанализированной строке, а все расширенные столбцы должны соответствовать требуемым типам.
        
    * *Regex*: стрингконстант может быть регулярным выражением, а совпадение — нечетким. Все строковые разделители должны присутствовать в проанализированной строке, а все расширенные столбцы должны соответствовать требуемым типам. Разделители строк могут быть регулярными выражениями для этого режима.
    
    * *flags*: flags для использования в режиме Regex: `U` (нежадный), `m` (многострочный режим), `s` (совпадение с новой строкой) `\n` , `i` (без учета регистра) дополнительные флаги можно найти в [RE2 flags](re2.md).
        
* *Выражение*: выражение, результатом которого является строка.

* *ColumnName:* Имя столбца, присвоенного значению, взятому из строкового выражения. 
  
* *ColumnType:* должен быть необязательным скалярным типом, указывающим тип, в который нужно преобразовать значение. Значение по умолчанию — строковый тип.

**Возвращает**

Входная таблица, которая расширяется в соответствии со списком столбцов, предоставленных оператору.

> [!Note] 
> В выходных данных будут только успешно проанализированные строки. Строки, которые не соответствуют шаблону, будут отфильтрованы.

**"Советы"**

* `parse-where`анализирует строки так же, как [синтаксический анализ](parseoperator.md)и отфильтровывает строки, которые не были успешно проанализированы.

* Используйте [Project](projectoperator.md) , если также нужно удалить или переименовать некоторые столбцы.

* Используйте * в шаблоне, чтобы пропустить нежелательные значения. Это значение нельзя использовать после строкового столбца.

* Шаблон анализа может начинаться с *ColumnName*и в дополнение к *стрингконстант*. 

* Если проанализированное *выражение* не имеет строкового типа, оно будет преобразовано в тип String.

* Если используется режим Regex, можно добавить флаги Regex для управления целым регулярным выражением, используемым при синтаксическом анализе.

* В режиме Regex синтаксический анализ преобразует шаблон в регулярное выражение и использует [синтаксис RE2](re2.md) для выполнения сопоставления с использованием пронумерованных захваченных групп, которые обрабатываются внутренним образом.
  
  Например, эта инструкция Parse:
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Регулярное выражение, которое будет создаваться синтаксическим анализом, имеет значение `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    - `*`был преобразован в `.*?` .
        
    - `string`был преобразован в `.*?` .
        
    - `long`был преобразован в `\-\d+` .

## <a name="examples"></a>Примеры

`parse-where`Оператор предоставляет упрощенный способ для `extend` таблицы, используя несколько `extract` приложений в одном `string` выражении. Это наиболее полезно, если в таблице есть `string` столбец, содержащий несколько значений, которые необходимо разделить на отдельные столбцы. Например, можно разбить столбец, созданный оператором трассировки разработчика ("" `printf` `Console.WriteLine` ).

### <a name="using-parse"></a>Использование `parse`

В приведенном ниже примере столбец `EventText` таблицы `Traces` содержит строки в форме `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` . В следующей операции будет расширена таблица с шестью столбцами: `resourceName` ,,,,,, `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` `Month` и `Day` . 

Некоторые строки не имеют полного соответствия.

При использовании `parse` вычисляемые столбцы будут иметь значения NULL.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|тоталслицес|слиценумбер|локктиме|релеасетиме|превиауслокктиме|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|пипелинесчедулер|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

### <a name="using-parse-where"></a>Использование `parse-where` 

Использование инструкции "Parse-Where" отфильтровывает неудачно проанализированные строки из результата.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=invalid_number, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=invalid_datetime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=invalid_number, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|тоталслицес|слиценумбер|локктиме|релеасетиме|превиауслокктиме|
|---|---|---|---|---|---|
|пипелинесчедулер|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


### <a name="regex-mode-using-regex-flags"></a>Режим Regex с использованием флагов Regex

Чтобы получить resourceName и Тоталслицес, используйте следующий запрос:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

### <a name="parse-where-with-case-insensitive-regex-flag"></a>`parse-where`с флагом Regex без учета регистра

В приведенном выше запросе в режиме по умолчанию учитывается регистр, поэтому строки были успешно проанализированы. Результат не получен.

Чтобы получить необходимый результат, выполните команду `parse-where` с флагом Regex без учета регистра ( `i` ).

Будут успешно проанализированы только три строки, поэтому результат будет состоять из трех записей (некоторые Тоталслицес содержат недопустимые целые числа).

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=11, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=non_valid_integer, sliceNumber=44, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse-where kind = regex flags=i EventText with * "RESOURCENAME=" resourceName "," * "totalSlices=" totalSlices:long "," *
| project resourceName, totalSlices
```

|resourceName|тоталслицес|
|---|---|
|пипелинесчедулер|27|
|пипелинесчедулер|27|
|пипелинесчедулер|27|
