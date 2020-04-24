---
title: оператор анализа - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается оператор анализа в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: beb51ac80810e5d14013e9b3571d759bb7b09125
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511515"
---
# <a name="parse-operator"></a>Оператор parse

оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Для неудачно разогнанных строк расчетные столбцы будут иметь нулевую.
Смотрите [разбор,где](parsewhereoperator.md) оператор, который отфильтровывает неудачно разогнанные строки.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**Синтаксис**

*Т.* `| parse` `kind=regex` `flags=regex_flags``simple` | `*` *Expression* `*``:` *StringConstant* *ColumnName* *ColumnType*Выражение (StringConstant ColumnName - ColumnType) ... `with` `relaxed`

**Аргументы**

* *T*: Таблица ввода.
* Вид: 

    * простой (по умолчанию) : StringConstant является регулярным значениестрок и матч является строгим, что означает, что все строки делимитеров должны отображаться в разогнанной строке и все расширенные столбцы должны соответствовать требуемым типам.
        
    * regex : StringConstant может быть регулярным выражением, и матч строгий, что означает, что все делимитемые строки (которые могут быть regex для этого режима) должны отображаться в разборной строке, и все расширенные столбцы должны соответствовать требуемым типам.
    
    * флаги : Флаги, которые `U` будут использоваться в `m` режиме regex, как (Ungreedy), (многолинейный `s` режим), (матч новой `\n`линии), `i` (случай-нечувствительный) и многое другое в [RE2 флаги](re2.md).
        
    * расслабленный : StringConstant является регулярным значениестроки и матч расслаблен, что означает, что все строки делимитеров должны отображаться в разображенной строки, но расширенные столбцы могут соответствовать необходимых типов частично (расширенные столбцы, которые не соответствуют требуемым типам получит значение нуля).

* *Выражение*: Выражение, которое оценивает строку.

* *КолонкаИмя:* Имя столбца для присвоения значения (выведенного из выражения строки). 
  
* *ColumnType:* должен быть факультативным типом масштабирования, который указывает тип для преобразования значения в (по умолчанию это тип строки).

**Возвращает**

Таблица ввода, расширенная в соответствии со списком столбцов, которые предоставляются оператору.

**Советы**

* Используйте, [`project`](projectoperator.md) если вы также хотите, чтобы падение или переименовать некоторые столбцы.

* Используйте в шаблоне для того, чтобы пропустить значения нежелательной (не может быть использован после столбца строки)

* Шаблон разбора может начинаться с *ColumnName* и не только с *StringConstant*. 

* Если разобранное *выражение* не является строкой типа, оно будет преобразовано в строку типа.

* При использовании режима regex существует возможность добавления флагов regex для управления всем регесом, используемым в разборе.

* В режиме regex, разбор будет переводить шаблон на regex и использовать [re2 синтаксис](re2.md) для того, чтобы сделать сопоставление с помощью пронумерованных захваченных групп, которые обрабатываются внутренне.
  Так, например, это заявление разбора:
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Regex, который будет генерироваться разбором внутри `.*?<regex1>(.*?)<regex2>(\-\d+)`.
        
    - `*`был переведен `.*?`на .
        
    - `string`был переведен `.*?`на .
        
    - `long`был переведен `\-\d+`на .

**Примеры**

Оператор `parse` предоставляет обтекаемый путь `extend` `extract` к таблице, `string` используя несколько приложений на одном и том же выражении.
Это наиболее полезно, `string` когда в таблице есть столбец, содержащий несколько значений, которые необходимо разбить на`printf`отдельные`Console.WriteLine`столбцы, например столбец, созданный прослеживаемым микрозапросом (""/" ") оператора.

В приведенном ниже примере `EventText` предположим, что столбец таблицы `Traces` содержит строки формы. `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`
Операция ниже расширит таблицу с `resourceName` `totalSlices`6 `sliceNumber` `lockTime `столбцов: `Month` `Day`, , , `releaseTime` `previouLockTime`, и . 


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

|resourceName|всегоФрагменты|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|КонвейерПланировщик|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|КонвейерПланировщик|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|КонвейерПланировщик|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|КонвейерПланировщик|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|КонвейерПланировщик|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

для режима regex :

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

|resourceName|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|
|КонвейерПланировщик|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|КонвейерПланировщик|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|КонвейерПланировщик|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|КонвейерПланировщик|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|КонвейерПланировщик|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


для режима regex с использованием флагов regex:

если мы заинтересованы в получении только ресурса, и мы используем этот запрос:

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
|PipelineScheduler, totalSlices-27, sliceNumber-23, lockTime-02/17/2016 08:40:01, releaseTime-02/17/2016 08:40:01|
|PipelineScheduler, totalSlices-27, sliceNumber-15, lockTime-02/17/2016 08:40:00, releaseTime-02/17/2016 08:40:00|
|PipelineScheduler, totalSlices-27, sliceNumber-20, lockTime-02/17/2016 08:40:01, releaseTime-02/17/2016 08:40:01|
|PipelineScheduler, totalSlices-27, sliceNumber-22, lockTime-02/17/2016 08:41:01, releaseTime-02/17/2016 08:41:00|
|PipelineScheduler, totalSlices-27, sliceNumber-16, lockTime-02/17/2016 08:41:00, releaseTime-02/17/2016 08:41:00|

Но мы не получаем ожидаемых результатов, так как режим по умолчанию жадный.
или даже если у нас было несколько записей, где resourceName появляется иногда ниже случае иногда верхний случай, чтобы мы могли получить nulls для некоторых значений.
для того, чтобы получить разыскиваемый результат, мы`U`можем запустить этот с`i`нежадной ( ) и отключить случае чувствительных ( ) regex флаги:

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
|КонвейерПланировщик|
|КонвейерПланировщик|
|КонвейерПланировщик|
|КонвейерПланировщик|
|КонвейерПланировщик|


Если разогнанная строка имеет новые `s` линии, вы должны использовать флаг для разбора текста, как ожидалось:

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

|resourceName|всегоФрагменты|lockTime|releaseTime|предыдущееLockTime|
|---|---|---|---|---|
|КонвейерПланировщик<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|КонвейерПланировщик<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|КонвейерПланировщик<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|КонвейерПланировщик<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|КонвейерПланировщик<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


в этом примере для расслабленного режима: расширенный столбец totalSlices должен быть длинным типом, но в разобранной строке он имеет значение nonValidLongValue.
колонка releaseTime имеет ту же проблему, значение nonValidDateTime не может быть разогнано как время даты.
в этом случае эти две расширенные столбцы получат значение нулевым, в то время как другие (например, sliceNumber) по-прежнему получают правильные значения.

использование доброго - простого для того же запроса ниже дает нулевой для всех расширенных столбцов, потому что это строго на расширенных столбцах (это разница между расслабленным и простым режимом, в расслабленном режиме, расширенные столбцы могут быть сопоставлены частично).

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

|resourceName|всегоФрагменты|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|КонвейерПланировщик|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|КонвейерПланировщик|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|КонвейерПланировщик||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|КонвейерПланировщик||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|КонвейерПланировщик|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|