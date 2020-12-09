---
title: Оператор parse — Azure Data Explorer
description: Узнайте, как использовать оператор parse в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 2b034719fa7c2f3714020c722b5717f5cf8590ff
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512967"
---
# <a name="parse-operator"></a>Оператор parse

оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Вычисляемые столбцы будут иметь значения NULL для неудачно проанализированных строк.
Дополнительные сведения см. в статье [Оператор parse-where](parsewhereoperator.md).

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>Синтаксис

*T* `| parse` [`kind=regex` [`flags=regex_flags`] |`simple`|`relaxed`] *Expression* `with` `*` (*StringConstant* *ColumnName* [`:` *ColumnType*]) `*`...

## <a name="arguments"></a>Аргументы

* *T* — входная таблица.
* kind:

    * simple (по умолчанию) — StringConstant — это значение строки регулярного выражения, и соответствие является строгим. Все строковые разделители должны присутствовать в проанализированной строке, и все расширенные столбцы должны соответствовать требуемым типам.
        
    * regex — StringConstant может быть регулярным выражением, и соответствие является строгим. Все строковые разделители, которые могут быть регулярными выражениями при использовании этого режима, должны присутствовать в проанализированной строке, и все расширенные столбцы должны соответствовать требуемым типам.
    
    * flags — флаги для использования в режиме регулярных выражений, например `U` (нежадный), `m` (многострочный режим), `s` (соответствие для символов перехода строки `\n`), `i` (без учета регистра) во [флагах RE2](re2.md).
        
    * relaxed — StringConstant — это значение строки регулярного выражения, и соответствие является нестрогим. Все строковые разделители должны присутствовать в проанализированной строке, но расширенные столбцы могут лишь частично соответствовать требуемым типам. Расширенные столбцы, которые не соответствуют требуемым типам, получат значение NULL.

* *Expression* (Выражение). Выражение, результатом которого является строка.

* *ColumnName.* Имя столбца, которому назначается значение. Извлекается из строкового выражения. 
  
* *ColumnType.* Необязательный параметр. Скалярное значение, указывающее тип, в который необходимо преобразовать значение. Значение по умолчанию — тип `string`.

## <a name="returns"></a>Возвращаемое значение

Входная таблица, развернутая в соответствии со списком столбцов, которые были предоставлены оператором.

**Советы**

* Используйте [`project`](projectoperator.md), если необходимо удалить или переименовать некоторые столбцы.

* Используйте * в шаблоне, чтобы пропустить ненужные значения. 

    > [!NOTE] 
    > `*` нельзя использовать после столбца с типом `string`.

* Шаблон анализа может начинаться с *ColumnName*, а не только со *StringConstant*.

* Если проанализированное *выражение* не имеет тип `string`, оно будет преобразовано в `string`.

* Если используется режим регулярных выражений, можно добавить флаги регулярных выражений для управления всем регулярным выражением, которое используется при анализе.

* В режиме регулярных выражений в ходе анализа шаблон преобразуется в регулярное выражение. Используйте [синтаксис RE2](re2.md), чтобы выполнить сопоставление, а также используйте нумерованные записанные группы, которые обрабатываются внутренними средствами.
    Пример:

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    В инструкции parse регулярное выражение, создаваемое внутренним анализом, имеет вид `.*?<regex1>(.*?)<regex2>(\-\d+)`.
        
    * `*` преобразовано в `.*?`.
        
    * `string` преобразовано в `.*?`.
        
    * `long` преобразовано в `\-\d+`.

## <a name="examples"></a>Примеры

Оператор `parse` предоставляет упрощенный способ использования `extend` для таблицы путем применения нескольких операторов `extract` в одном выражении `string`. Этот результат полезен, если в таблице есть столбец типа `string`, содержащий несколько значений, которые необходимо разделить на отдельные столбцы. Например, столбец, созданный инструкцией трассировки разработчика ("`printf`"/"`Console.WriteLine`").

В примере ниже предполагается, что столбец `EventText` таблицы `Traces` содержит строки вида `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`.
Оператор включит в таблицу шесть столбцов: `resourceName`, `totalSlices`, `sliceNumber`, `lockTime `, `releaseTime`, `previousLockTime`, `Month` и `Day`. 

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
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**Для режима регулярных выражений**

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
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|

**Для режима регулярных выражений с использованием флагов регулярных выражений**

Если вы хотите получать только значение resourceName, используйте следующий запрос:

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
|PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00|
|PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01|
|PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00|
|PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00|

Вы не получите ожидаемые результаты, так как по умолчанию используется жадный режим.
Если есть несколько записей, в которых *resourceName* иногда указывается в нижнем регистре, а иногда в верхнем, вы можете получить NULL для некоторых значений.

Чтобы получить нужный результат, выполните запрос в нежадном режиме `U` и отключите флаги регулярных выражений `i` с учетом регистра.

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
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|

Если анализируемая строка содержит символы перевода строки, используйте флаг `s` для обработки текста.

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

|resourceName|totalSlices|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**Нестрогий режим**

В этом примере для нестрогого режима расширенный столбец *totalSlices* должен иметь тип `long`. Но в проанализированной строке он имеет значение *nonValidLongValue*.
В расширенном столбце *releaseTime* значение *nonValidDateTime* не может быть проанализировано как *datetime*.
Эти два расширенных столбца получат значение NULL, а другие, например *sliceNumber*, получат правильные значения.

Если вы укажете параметр *kind = simple* для этого же запроса ниже, вы получите значение NULL для всех расширенных столбцов. Этот параметр является строгим для расширенных столбцов и представляет разницу между нестрогим и простым режимами.

 > [!NOTE] 
 > В нестрогом режиме расширенные столбцы могут частично сопоставляться.

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
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
 
