---
title: подключаемый модуль diffpatterns — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль diffpatterns в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f269bb12c4e4f73f7a7e6c4e9818d47dfc8002ef
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249490"
---
# <a name="diff-patterns-plugin"></a>подключаемый модуль шаблонов diff

Сравнивает два набора данных одной структуры и находит закономерности дискретных атрибутов (измерений), характеризующие различия между двумя наборами данных.
 `Diffpatterns` был разработан для анализа сбоев (например, при сравнении сбоев и неудачных попыток в заданный промежуток времени), но потенциально может найти различия между любыми двумя наборами данных одной и той же структуры. 

```kusto
T | evaluate diffpatterns(splitColumn)
```
> [!NOTE]
> `diffpatterns` нацелены на поиск существенных шаблонов (которые захватывают части различий данных между наборами) и не предназначены для построчных различий.

## <a name="syntax"></a>Синтаксис

`T | evaluate diffpatterns(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

## <a name="arguments"></a>Аргументы 

### <a name="required-arguments"></a>Обязательные аргументы

* SplitColumn - *column_name*

    Указывает алгоритму, как разбить запрос на наборы данных. Согласно указанным значениям для аргументов SplitValueA и SplitValueB (см. ниже) алгоритм разделяет запрос на два набора данных — A и B, а также анализирует различия между ними. Таким образом разделенный столбец должен содержать как минимум два различных значения.

* SplitValueA — *строка*

    Строковое представление одного из значений в указанном аргументе SplitColumn. Все строки, имеющие это значение в аргументах SplitColumn, считаются набором данных "A".

* SplitValueB — *строка*

    Строковое представление одного из значений в указанном аргументе SplitColumn. Все строки, имеющие это значение в аргументах SplitColumn, считаются набором данных «B».

    Например, `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `.

### <a name="optional-arguments"></a>Необязательные аргументы

Остальные аргументы необязательны, но они должны быть упорядочены, как показано ниже. Чтобы указать, что следует использовать значение по умолчанию, добавьте знак тильды в строку "~" (см. примеры ниже).

* WeightColumn — *column_name*

    Рассматривает каждую строку входных данных в соответствии с указанным весовым коэффициентом (по умолчанию весовой коэффициент каждой строки — 1). Аргумент должен быть именем числового столбца (например,,, `int` `long` `real` ).
    Как правило, при использовании столбца с весовым коэффициентом следует учитывать выборку, группирование или агрегирование данных, внедренных в каждой строке.
    
    Например, `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `.

* Пороговое значение — 0,015 < *double* < 1 [по умолчанию: 0,05]

    Задает минимальную разницу для шаблонов (соотношение) между двумя наборами.

    Пример: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* Максдименсионс-0 < *int* [по умолчанию: без ограничений]

    Задает максимальное число некоррелированных измерений на шаблон результатов. Указав предел, вы уменьшаете время выполнения запроса.

    Пример: `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard- *"любой-значение на тип"*

    Задает подстановочный знак для определенного типа в таблице результатов, который укажет, что текущий шаблон не имеет ограничения по этому столбцу.
    По умолчанию имеет значение NULL, для строки по умолчанию указывается пустая строка. Если значение по умолчанию является допустимым значением в данных, следует использовать другое подстановочное значение (например, `*` ).
    См. следующий пример.

    Например, `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`.

## <a name="returns"></a>Результаты

`Diffpatterns` Возвращает небольшой набор шаблонов, которые захватывают различные части данных в двух наборах (то есть шаблон, захватывает большой процент строк в первом наборе данных, и низкий процент строк во втором наборе). В результатах каждый шаблон соответствует строке.

Результат `diffpatterns` возвращает следующие столбцы:

* SegmentId: удостоверение, назначенное шаблону в текущем запросе (Примечание. идентификаторы не обязательно будут одинаковыми в повторяющихся запросах).

* Число: число строк, захваченных шаблоном в Set A (Set A является эквивалентом `where tostring(splitColumn) == SplitValueA` ).

* Каунтб: число строк, захваченных шаблоном в наборе B (Set B, эквивалентно `where tostring(splitColumn) == SplitValueB` ).

* Процентная доля: процент строк в наборе, захваченный шаблоном (100,0 * число/число (сета)).

* Перцентб: процент строк в наборе B, захваченный шаблоном (100,0 * Каунтб/Count (Сетб)).

* Перцентдиффаб: абсолютная разница в процентах между A и B (| Процент Перцентб |) — Это основная мера важности шаблонов в описании различий между двумя наборами.

* Остальные столбцы: являются исходной схемой входных данных и описывают шаблон. Каждая строка (шаблон) повторно пересылает пересечение значений, не являющихся подстановочными значениями столбцов (эквивалент `where col1==val1 and col2==val2 and ... colN=valN` для каждого значения, не являющегося подстановочным знаком в строке).

Для каждого шаблона столбцы, которые не заданы в шаблоне (то есть без ограничений на конкретное значение), будут содержать подстановочное значение, которое по умолчанию равно null. В разделе arguments ниже описано, как можно вручную изменить подстановочные знаки.

* Примечание. шаблоны часто не отличаются. Они могут перекрываться и обычно не охватывают все исходные строки. Некоторые строки могут не охватываться ни одним из шаблонов.

> [!TIP]
> * Используйте [WHERE](./whereoperator.md) и [Project](./projectoperator.md) во входном канале, чтобы уменьшить объем данных до того, что вас интересует.
> * Если вы хотите узнать дополнительные сведения о конкретной строке, добавьте ее значения в фильтр `where` .

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```

|Идентификатор сегмента|Число A|Число B|Процент A|Процент B|перцентдиффаб|Состояние|EventType|Источник|DamageCrops|
|---|---|---|---|---|---|---|---|---|---|
|0|2278|93|49,8|7.1|42,7||Град||0|
|1|779|512|17,03|39,08|22,05||Шквалистый ветер|||
|2|1098|118|24,01|9,01|15|||Подготовленный корректировщик|0|
|3|136|158|2,97|12,06|9,09|||Газета||
|4|359|214|7,85|16,34|8,49||Внезапное наводнение|||
|5|50|122|1,09|9,31|8,22|Айова||||
|6|655|279|14,32|21,3|6,98|||Правоприменение||
|7|150|117|3,28|8,93|5,65||Наводнение|||
|8|362|176|7,91|13,44|5,52|||Специалист по управлению в чрезвычайных ситуациях||
