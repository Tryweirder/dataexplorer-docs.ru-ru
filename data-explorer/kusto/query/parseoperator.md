---
title: Оператор Parse — Azure обозреватель данных
description: В этой статье описывается оператор Parse в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 2b034719fa7c2f3714020c722b5717f5cf8590ff
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512967"
---
# <a name="parse-operator"></a>Оператор parse

оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. Вычисляемые столбцы будут иметь значения NULL для неудачно проанализированных строк.
Дополнительные сведения см. в описании [оператора Parse-WHERE](parsewhereoperator.md).

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

## <a name="syntax"></a>Синтаксис

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ] *выражение* `with` `*` (*стрингконстант* *ColumnName* [ `:` *ColumnType*]) `*` ...

## <a name="arguments"></a>Аргументы

* *T*: входная таблица.
* особого

    * Simple (значение по умолчанию): Стрингконстант — это регулярное строковое значение, а соответствие —. Все строковые разделители должны присутствовать в проанализированной строке, а все расширенные столбцы должны соответствовать требуемым типам.
        
    * Regex: Стрингконстант может быть регулярным выражением, а совпадение — нечетким. Все строковые разделители, которые могут быть регулярными выражениями для этого режима, должны отображаться в проанализированной строке, а все расширенные столбцы должны соответствовать требуемым типам.
    
    * flags: флаги для использования в режиме Regex (например `U` , "нежадные"), `m` (многострочный режим), `s` (совпадение с новой строкой) `\n` , `i` (без учета регистра) в [флагах RE2](re2.md).
        
    * неявное: Стрингконстант — это регулярное строковое значение, и сопоставление является нестрогим. Все строковые разделители должны присутствовать в проанализированной строке, но расширенные столбцы могут частично соответствовать требуемым типам. Расширенные столбцы, которые не соответствуют требуемым типам, получат значение null.

* *Выражение*: выражение, результатом которого является строка.

* *ColumnName:* Имя столбца, которому присваивается значение, извлеченное из строкового выражения. 
  
* *ColumnType:* Используемых. Скалярное значение, указывающее тип, в который необходимо преобразовать значение. По умолчанию используется `string` тип.

## <a name="returns"></a>Возвращаемое значение

Входная таблица, расширенная в соответствии со списком столбцов, предоставленных оператору.

**Советы**

* Используйте [`project`](projectoperator.md) , если также требуется удалить или переименовать некоторые столбцы.

* Используйте * в шаблоне, чтобы пропустить нежелательные значения. 

    > [!NOTE] 
    > `*`Нельзя использовать после `string` столбца типа.

* Шаблон анализа может начинаться с *ColumnName* , а не только с *стрингконстант*.

* Если проанализированное *выражение* не имеет тип `string` , оно будет преобразовано в тип `string` .

* Если используется режим Regex, можно добавить флаги Regex для управления всем регулярным выражением, используемым в синтаксическом анализе.

* В режиме Regex Parse шаблон преобразуется в регулярное выражение. Используйте [синтаксис RE2](re2.md) для сопоставления и использования пронумерованных захваченных групп, которые обрабатываются внутренним образом.
    Пример:

    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    В инструкции Parse регулярное выражение, создаваемое синтаксическим анализом, имеет значение `.*?<regex1>(.*?)<regex2>(\-\d+)` .
        
    * `*` был преобразован в `.*?` .
        
    * `string` был преобразован в `.*?` .
        
    * `long` был преобразован в `\-\d+` .

## <a name="examples"></a>Примеры

`parse`Оператор предоставляет упрощенный способ для `extend` таблицы, используя несколько `extract` приложений в одном `string` выражении. Этот результат полезен, если в таблице есть `string` столбец, содержащий несколько значений, которые необходимо разделить на отдельные столбцы. Например, столбец, созданный оператором трассировки разработчика (" `printf` "/" `Console.WriteLine` ").

В приведенном ниже примере предполагается, что столбец `EventText` таблицы `Traces` содержит строки в форме `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
Операция расширяет таблицу с шестью столбцами: `resourceName` ,,,,,, `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previousLockTime` `Month` и `Day` . 

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

|resourceName|тоталслицес|слиценумбер|локктиме|релеасетиме|превиауслокктиме|
|---|---|---|---|---|---|
|пипелинесчедулер|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|пипелинесчедулер|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|пипелинесчедулер|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|пипелинесчедулер|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

**Для режима Regex**

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

|resourceName|слиценумбер|локктиме|релеасетиме|превиауслокктиме|
|---|---|---|---|---|
|пипелинесчедулер|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|пипелинесчедулер|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|пипелинесчедулер|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|пипелинесчедулер|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|пипелинесчедулер|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|

**Для режима Regex с использованием флагов Regex**

Если вы хотите получить только resourceName, используйте следующий запрос:

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

Ожидаемые результаты не будут получены, так как режим по умолчанию является жадным.
Если имеется несколько записей, в которых *resourceName*  иногда отображается в нижнем регистре, а иногда — в верхнем регистре, для некоторых значений могут быть определены значения NULL.

Чтобы получить желаемый результат, выполните запрос с нежадным `U` и отключите зависящие от регистра `i` Флаги Regex.

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

Если анализируемая строка содержит символы новой строки, используйте флаг `s` , чтобы проанализировать текст.

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

**Нестрогий режим**

В этом примере для неявного режима расширенный столбец *тоталслицес* должен иметь тип `long` . Однако в проанализированной строке она имеет значение *нонвалидлонгвалуе*.
В расширенном столбце *релеасетиме* значение *нонвалиддатетиме* не может быть проанализировано как *DateTime*.
Эти два расширенных столбца получают значение null, а другие, например *слиценумбер*, по-прежнему получают правильные значения.

Если для одного и того же запроса используется параметр *тип = Simple* , вы получите значение NULL для всех расширенных столбцов. Этот параметр является строгим для расширенных столбцов и является разностью между нечетким и простым режимами.

 > [!NOTE] 
 > В неявном режиме расширенные столбцы могут быть частично сопоставлены.

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

|resourceName|тоталслицес|слиценумбер|локктиме|релеасетиме|превиауслокктиме|
|---|---|---|---|---|---|
|пипелинесчедулер|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|пипелинесчедулер|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|пипелинесчедулер||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|пипелинесчедулер||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|пипелинесчедулер|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
 
