---
title: Оператор Parse — Azure обозреватель данных
description: В этой статье описывается оператор Parse в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8255f3d0c3dc0006029f06c7a0da4b41dfbaa1b7
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271345"
---
# <a name="parse-operator"></a>Оператор parse

оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Для неудачно проанализированных строк вычисляемые столбцы будут иметь значения NULL.
См. раздел оператор [Parse-WHERE](parsewhereoperator.md) , который отфильтровывает неудачно проанализированные строки.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**Синтаксис**

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ] *выражение* `with` `*` (*стрингконстант* *ColumnName* [ `:` *ColumnType*]) `*` ...

**Аргументы**

* *T*: входная таблица.
* особого 

    * Simple (значение по умолчанию): Стрингконстант — это регулярное строковое значение, а соответствие — это означает, что все строковые разделители должны отображаться в проанализированной строке, а все расширенные столбцы должны соответствовать требуемым типам.
        
    * Regex: Стрингконстант может быть регулярным выражением, а совпадение — ограничение, то есть все строковые разделители (которые могут быть регулярными выражениями для этого режима) должны отображаться в проанализированной строке, а все расширенные столбцы должны соответствовать требуемым типам.
    
    * flags: флаги для использования в режиме Regex (например, `U` "нежадные"), `m` (многострочный режим), `s` (совпадение с новой строкой) `\n` , `i` (без учета регистра) и больше в [флагах RE2](re2.md).
        
    * нестрогое: Стрингконстант — это регулярное строковое значение, а соответствие является нестрогим. Это означает, что все строковые разделители должны отображаться в проанализированной строке, но расширенные столбцы могут точно соответствовать обязательным типам (расширенные столбцы, которые не соответствуют требуемым типам, получат значение null).

* *Выражение*: выражение, результатом которого является строка.

* *ColumnName:* Имя столбца, которому присваивается значение (взятое из строкового выражения). 
  
* *ColumnType:* должен быть необязательным скалярным типом, указывающим тип для преобразования значения (по умолчанию это строковый тип).

**Возвращает**

Входная таблица, расширенная в соответствии со списком столбцов, предоставленных оператору.

**"Советы"**

* Используйте [`project`](projectoperator.md) , если также требуется удалить или переименовать некоторые столбцы.

* Используйте * в шаблоне, чтобы пропустить нежелательные значения (не может использоваться после строкового столбца)

* Шаблон анализа может начинаться с *ColumnName* , а не только с *стрингконстант*. 

* Если проанализированное *выражение* не имеет строкового типа, оно будет преобразовано в тип String.

* Если используется режим Regex, можно добавить флаги Regex, чтобы управлять целым регулярным выражением, используемым в синтаксическом анализе.

* В режиме Regex синтаксический анализ преобразует шаблон в регулярное выражение и использует [синтаксис RE2](re2.md) , чтобы выполнить сопоставление с использованием пронумерованных захваченных групп, которые обрабатываются внутренним образом.
  Например, эта инструкция Parse:
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Регулярное выражение, которое будет создаваться синтаксическим анализом, имеет значение `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    - `*`был преобразован в `.*?` .
        
    - `string`был преобразован в `.*?` .
        
    - `long`был преобразован в `\-\d+` .

**Примеры**

`parse`Оператор предоставляет упрощенный способ для `extend` таблицы, используя несколько `extract` приложений в одном `string` выражении.
Это наиболее полезно, если таблица `string` содержит столбец, содержащий несколько значений, которые необходимо приостановить на отдельные столбцы, например столбец, созданный оператором трассировки разработчика ("" `printf` `Console.WriteLine` ).

В приведенном ниже примере предполагается, что столбец `EventText` таблицы `Traces` содержит строки в форме `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
В следующей операции будет расширена таблица с 6 столбцами: `resourceName` , `totalSlices` ,,,, `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` `Month` и `Day` . 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|тоталслицес|слиценумбер|локктиме|релеасетиме|превиаулокктиме|
|---|---|---|---|---|---|
|пипелинесчедулер|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|пипелинесчедулер|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|пипелинесчедулер|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

для режима Regex:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previouLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|слиценумбер|локктиме|релеасетиме|превиаулокктиме|
|---|---|---|---|---|
|пипелинесчедулер|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|пипелинесчедулер|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|пипелинесчедулер|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|пипелинесчедулер|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|пипелинесчедулер|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


для режима Regex с использованием флагов Regex:

Если мы заинтересованы в получении только resourceName и используем этот запрос:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex  EventText with * "resourceName=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|Пипелинесчедулер, Тоталслицес = 27, Слиценумбер = 23, Локктиме = 02/17/2016 08:40:01, Релеасетиме = 02/17/2016 08:40:01|
|Пипелинесчедулер, Тоталслицес = 27, Слиценумбер = 15, Локктиме = 02/17/2016 08:40:00, Релеасетиме = 02/17/2016 08:40:00|
|Пипелинесчедулер, Тоталслицес = 27, Слиценумбер = 20, Локктиме = 02/17/2016 08:40:01, Релеасетиме = 02/17/2016 08:40:01|
|Пипелинесчедулер, Тоталслицес = 27, Слиценумбер = 22, Локктиме = 02/17/2016 08:41:01, Релеасетиме = 02/17/2016 08:41:00|
|Пипелинесчедулер, Тоталслицес = 27, Слиценумбер = 16, Локктиме = 02/17/2016 08:41:00, Релеасетиме = 02/17/2016 08:41:00|

Но мы не получаем ожидаемых результатов, так как режим по умолчанию является жадным.
или даже при наличии небольшого количества записей, в которых resourceName имеет место, иногда в верхнем регистре, поэтому для некоторых значений могут быть получены значения NULL.
чтобы получить желаемый результат, мы можем выполнить его с помощью нежадных ( `U` ) и отключить флаги Regex с учетом регистра ( `i` ):

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex flags = Ui EventText with * "RESOURCENAME=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|пипелинесчедулер|
|пипелинесчедулер|
|пипелинесчедулер|
|пипелинесчедулер|
|пипелинесчедулер|


Если анализируемая строка содержит символы новой строки, следует использовать флаг `s` для синтаксического анализа текста, как ожидалось:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=23\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=15\nlockTime=02/17/2016 08:40:00\nreleaseTime=02/17/2016 08:40:00\npreviousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=20\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=22\nlockTime=02/17/2016 08:41:01\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=16\nlockTime=02/17/2016 08:41:00\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=regex flags=s EventText with * "resourceName=" resourceName:string "(.*?)totalSlices=" totalSlices:long "(.*?)lockTime=" lockTime:datetime "(.*?)releaseTime=" releaseTime:datetime "(.*?)previousLockTime=" previousLockTime:datetime "\\)" 
| project-away EventText
```

|resourceName|тоталслицес|локктиме|релеасетиме|превиауслокктиме|
|---|---|---|---|---|
|пипелинесчедулер<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|пипелинесчедулер<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|пипелинесчедулер<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


в этом примере для неявного режима: Тоталслицес расширенный столбец должен иметь тип long, но в проанализированной строке он имеет значение Нонвалидлонгвалуе.
в расширенном столбце Релеасетиме возникла та же ошибка. значение Нонвалиддатетиме не может быть проанализировано как DateTime.
в этом случае эти два расширенных столбца получат значение null, а другие (например, Слиценумбер) по-прежнему получают правильные значения.

Использование типа = Simple для того же запроса, приведенного ниже, дает значение NULL для всех расширенных столбцов, поскольку оно является строгим для расширенных столбцов (это различие между нечетким и простым режимами, в неявном режиме, расширенные столбцы могут быть сопоставлены частично).

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=nonValidDateTime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *
| project-away EventText
```

|resourceName|тоталслицес|слиценумбер|локктиме|релеасетиме|превиаулокктиме|
|---|---|---|---|---|---|
|пипелинесчедулер|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|пипелинесчедулер|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|пипелинесчедулер||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|пипелинесчедулер||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|пипелинесчедулер|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
