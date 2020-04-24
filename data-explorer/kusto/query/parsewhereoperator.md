---
title: разбор-где оператор - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается разбор, где оператор в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/12/2020
ms.openlocfilehash: 0e44ab83242fc8aed0e46bdab1fa5a142992bfe5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511413"
---
# <a name="parse-where-operator"></a>Оператор parse-where

оценивает выражение строки и разбирает его на один или несколько вычисляемых столбцов. В результате получается только успешно разогнанные строки.
См. оператор [разбора,](parseoperator.md) который производит нули для неудачно разогнанных строк.

```kusto
T | parse-where Text with "ActivityName=" name ", ActivityType=" type
```

**Синтаксис**

*Т.* `| parse-where` `kind=regex` `flags=regex_flags``simple` *Expression* `with` Выражение `*` (*StringConstant* `:` *ColumnName* - `*` *ColumnType*) ...

**Аргументы**

* *T*: Таблица ввода.

* Вид: 

    * простой (по умолчанию) : StringConstant является регулярным значениестрок и матч является строгим, что означает, что все строки делимитеров должны отображаться в разогнанной строке и все расширенные столбцы должны соответствовать требуемым типам.
        
    * regex : StringConstant может быть регулярным выражением, и матч строгий, что означает, что все делимитемые строки (которые могут быть regex для этого режима) должны отображаться в разборной строке, и все расширенные столбцы должны соответствовать требуемым типам.
    
    * флаги : Флаги, которые `U` будут использоваться в `m` режиме regex, как (Ungreedy), (многолинейный `s` режим), (матч новой `\n`линии), `i` (случай-нечувствительный) и многое другое в [RE2 флаги](re2.md).
        
* *Выражение*: Выражение, которое оценивает строку.

* *КолонкаИмя:* Имя столбца для присвоения значения (выведенного из выражения строки). 
  
* *ColumnType:* должен быть факультативным типом масштабирования, который указывает тип для преобразования значения в (по умолчанию это тип строки).

**Возвращает**

Таблица ввода, расширенная в соответствии со списком столбцов, которые предоставляются оператору.
Только успешно разогнанные строки будут в выходе. строки, не совпадающие с шаблоном, будут отфильтрочены.

**Советы**

* `parse-where`разбирает строки таким же образом, что [разобрать,](parseoperator.md) но в дополнение фильтры из строк, которые не были разогнаны успешно.

* Используйте, [`project`](projectoperator.md) если вы также хотите, чтобы падение или переименовать некоторые столбцы.

* Используйте в шаблоне для того, чтобы пропустить значения нежелательной (не может быть использован после столбца строки)

* Шаблон разбора может начинаться с *ColumnName* и не только с *StringConstant*. 

* Если разобранное *выражение* не является строкой типа, оно будет преобразовано в строку типа.

* При использовании режима regex существует возможность добавления флагов regex для управления всем регесом, используемым в разборе.

* В режиме regex, разбор будет переводить шаблон на regex и использовать [re2 синтаксис](re2.md) для того, чтобы сделать сопоставление с помощью пронумерованных захваченных групп, которые обрабатываются внутренне.
  
  Так, например, это заявление разбора:
  
    ```kusto
    parse-where kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    Regex, который будет генерироваться разбором внутри `.*?<regex1>(.*?)<regex2>(\-\d+)`.
        
    - `*`был переведен `.*?`на .
        
    - `string`был переведен `.*?`на .
        
    - `long`был переведен `\-\d+`на .

**Примеры**

Оператор `parse-where` предоставляет обтекаемый путь `extend` `extract` к таблице, `string` используя несколько приложений на одном и том же выражении.
Это наиболее полезно, `string` когда в таблице есть столбец, содержащий несколько значений, которые необходимо разбить на`printf`отдельные`Console.WriteLine`столбцы, например столбец, созданный прослеживаемым микрозапросом (""/" ") оператора.

В приведенном ниже примере `EventText` предположим, что столбец таблицы `Traces` содержит строки формы. `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})`
Операция ниже расширит таблицу с `resourceName` `totalSlices`6 `sliceNumber` `lockTime `столбцов: `Month` `Day`, , , `releaseTime` `previouLockTime`, и . 

Здесь несколько строк не имеют полного соответствия.
Использование, `parse`рассчитанные столбцы будут иметь нулевую:

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
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|всегоФрагменты|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|||||||
|||||||
|||||||
|КонвейерПланировщик|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|КонвейерПланировщик|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


Использование `parse-where` будет фильтровать из неудачно разогнанных строк из результата:

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
| parse-where EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|всегоФрагменты|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|КонвейерПланировщик|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|КонвейерПланировщик|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


для режима regex с использованием флагов regex:

если мы заинтересованы в получении resouceName и totalSlices, и мы используем этот запрос:

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

В приведенном выше запросе мы не получаем результата, так как режим по умолчанию является чувствительным к инциденту, поэтому ни одна из строк не была успешно разогнана.

для того, чтобы получить необходимый `parse-where` результат, мы можем`i`запустить с нечувствительным случаем () флаг regex.
Обратите внимание, что только 3 строки будут разогнаны успешно и, следовательно, результат 3 записи (некоторые totalSlices имеет недействительными integers): 

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

|resourceName|всегоФрагменты|
|---|---|
|КонвейерПланировщик|27|
|КонвейерПланировщик|27|
|КонвейерПланировщик|27|


