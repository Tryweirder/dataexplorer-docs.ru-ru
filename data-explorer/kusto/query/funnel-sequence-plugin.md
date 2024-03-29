---
title: подключаемый модуль funnel_sequence — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль funnel_sequence в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 34159fb6d02cd30907924109c861d5e9fd963568
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252333"
---
# <a name="funnel_sequence-plugin"></a>Подключаемый модуль funnel_sequence

Вычисляет число различных пользователей, получивших последовательность состояний, и распределение предыдущего или следующего состояния, за которым следует последовательность. 

```kusto
T | evaluate funnel_sequence(id, datetime_column, startofday(ago(30d)), startofday(now()), 10m, 1d, state_column, dynamic(['S1', 'S2', 'S3']))
```

## <a name="syntax"></a>Синтаксис

*T* `| evaluate` `funnel_sequence(` *идколумн* `,` *тимелинеколумн* `,` *Start* `,` *End* `,` *макссекуенцестепвиндов*, *шаг*, *статеколумн*, *последовательность*`)`

## <a name="arguments"></a>Аргументы

* *T*: Входное табличное выражение.
* *Идколум*: ссылка на столбец должна присутствовать в исходном выражении.
* *Тимелинеколумн*: ссылка на столбец, представляющая временную шкалу, должна присутствовать в исходном выражении.
* *Start*: скалярное константное значение начального периода анализа.
* *End*: скалярное значение периода окончания анализа.
* *Макссекуенцестепвиндов*: скалярное постоянное значение максимального допустимого интервала времени между двумя последовательными шагами в последовательности.
* *Шаг*: скалярное постоянное значение периода шага анализа (bin).
* *Статеколумн*: ссылка на столбец, представляющая состояние, должна присутствовать в исходном выражении.
* *Sequence*: константный динамический массив со значениями последовательности (значения ищутся в `StateColumn` ).

## <a name="returns"></a>Результаты

Возвращает три выходных таблицы, которые полезны для создания диаграммы Sankey для проанализированной последовательности:

* Таблица #1 — Предыдущая — последовательность — следующий `dcount` тимелинеколумн: предыдущее состояние (может быть пустым, если существовали какие-либо пользователи с событиями только для искомой последовательности, но не для всех событий до нее). 
    Next: следующее состояние (может быть пустым, если существовали какие-либо пользователи с событиями только для искомой последовательности, но не для событий, которые следуют за ним). 
    `dcount`: число различных элементов `IdColumn` в временном окне, которые были перенесены `prev`  -->  `Sequence`  -->  `next` . 
    Примеры: массив идентификаторов (от `IdColumn` ), соответствующий последовательности строки (возвращается не более 128 идентификаторов). 

* Таблица #2 — Предыдущая последовательность `dcount` . проанализированное временное окно: предыдущее состояние (может быть пустым, если существовали какие-либо пользователи с событиями только для поисковой последовательности, но не с событиями до нее). 
    `dcount`: число различных элементов `IdColumn` в временном окне, которые были перенесены `prev`  -->  `Sequence`  -->  `next` . 
    Примеры: массив идентификаторов (от `IdColumn` ), соответствующий последовательности строки (возвращается не более 128 идентификаторов). 

* Таблица #3-Sequence — следующий `dcount` тимелинеколумн: анализируемое временное окно: следующее состояние (может быть пустым, если существовали какие-либо пользователи с событиями только для искомой последовательности, но не с другими событиями, которые следуют за ним). 
    `dcount`: число различных элементов `IdColumn` в временном окне, которые были перенесены `prev`  -->  `Sequence`  -->  `next` .
    Примеры: массив идентификаторов (от `IdColumn` ), соответствующий последовательности строки (возвращается не более 128 идентификаторов). 


## <a name="examples"></a>Примеры

### <a name="exploring-storm-events"></a>Просмотр событий с более подробной информацией 

Следующий запрос просматривает таблицу Стормевентс (статистику погоды для 2007) и показывает, какие события произошли до/после всех событий торнадо, произошедших в 2007.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Looking on StormEvents statistics: 
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

Результат включает три таблицы:

* #1 таблиц: все возможные варианты, которые были выполнены до и после последовательности. Например, вторая строка означает, что существовало 87 различных событий, имеющих следующую последовательность: `Hail` -> `Tornado` -> `Hail`


|`StartTime`|`prev`|`next`|`dcount`|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|||293|
|2007-01-01 00:00:00.0000000|Град|Град|87|
|2007-01-01 00:00:00.0000000|Шквалистый ветер|Шквалистый ветер|77|
|2007-01-01 00:00:00.0000000|Град|Шквалистый ветер|28|
|2007-01-01 00:00:00.0000000|Град||28|
|2007-01-01 00:00:00.0000000||Град|27|
|2007-01-01 00:00:00.0000000||Шквалистый ветер|25|
|2007-01-01 00:00:00.0000000|Шквалистый ветер|Град|24|
|2007-01-01 00:00:00.0000000|Шквалистый ветер||24|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|Внезапное наводнение|12|
|2007-01-01 00:00:00.0000000|Шквалистый ветер|Внезапное наводнение|8|
|2007-01-01 00:00:00.0000000|Внезапное наводнение||8|
|2007-01-01 00:00:00.0000000|Воронка Cloud|Шквалистый ветер|6|
|2007-01-01 00:00:00.0000000||Воронка Cloud|6|
|2007-01-01 00:00:00.0000000||Внезапное наводнение|6|
|2007-01-01 00:00:00.0000000|Воронка Cloud|Воронка Cloud|6|
|2007-01-01 00:00:00.0000000|Град|Внезапное наводнение|4|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|Шквалистый ветер|4|
|2007-01-01 00:00:00.0000000|Град|Воронка Cloud|4|
|2007-01-01 00:00:00.0000000|Воронка Cloud|Град|4|
|2007-01-01 00:00:00.0000000|Воронка Cloud||4|
|2007-01-01 00:00:00.0000000|Шквалистый ветер|Воронка Cloud|3|
|2007-01-01 00:00:00.0000000|Тяжелая дождя|Шквалистый ветер|2|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|Воронка Cloud|2|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|Град|2|
|2007-01-01 00:00:00.0000000|Строгая обмотка|Шквалистый ветер|1|
|2007-01-01 00:00:00.0000000|Тяжелая дождя|Внезапное наводнение|1|
|2007-01-01 00:00:00.0000000|Тяжелая дождя|Град|1|
|2007-01-01 00:00:00.0000000|Град|Наводнение|1|
|2007-01-01 00:00:00.0000000|Молнии|Град|1|
|2007-01-01 00:00:00.0000000|Тяжелая дождя|Молнии|1|
|2007-01-01 00:00:00.0000000|Воронка Cloud|Тяжелая дождя|1|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|Наводнение|1|
|2007-01-01 00:00:00.0000000|Наводнение|Внезапное наводнение|1|
|2007-01-01 00:00:00.0000000||Тяжелая дождя|1|
|2007-01-01 00:00:00.0000000|Воронка Cloud|Молнии|1|
|2007-01-01 00:00:00.0000000|Молнии|Шквалистый ветер|1|
|2007-01-01 00:00:00.0000000|Наводнение|Шквалистый ветер|1|
|2007-01-01 00:00:00.0000000|Град|Молнии|1|
|2007-01-01 00:00:00.0000000||Молнии|1|
|2007-01-01 00:00:00.0000000|Тропический огонь|Ураган (Тайфун)|1|
|2007-01-01 00:00:00.0000000|Коастал Flood||1|
|2007-01-01 00:00:00.0000000|Текущая RIP||1|
|2007-01-01 00:00:00.0000000|Тяжелая снег||1|
|2007-01-01 00:00:00.0000000|Строгая обмотка||1|

* Таблица #2: показывает все отдельные события, сгруппированные по предыдущему событию. Например, вторая строка показывает, что до этого произошло всего 150 событий `Hail` `Tornado` .

|`StartTime`|`prev`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||331|
|2007-01-01 00:00:00.0000000|Град|150|
|2007-01-01 00:00:00.0000000|Шквалистый ветер|135|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|28|
|2007-01-01 00:00:00.0000000|Воронка Cloud|22|
|2007-01-01 00:00:00.0000000|Тяжелая дождя|5|
|2007-01-01 00:00:00.0000000|Наводнение|2|
|2007-01-01 00:00:00.0000000|Молнии|2|
|2007-01-01 00:00:00.0000000|Строгая обмотка|2|
|2007-01-01 00:00:00.0000000|Тяжелая снег|1|
|2007-01-01 00:00:00.0000000|Текущая RIP|1|
|2007-01-01 00:00:00.0000000|Коастал Flood|1|
|2007-01-01 00:00:00.0000000|Тропический огонь|1|

* Таблица #3: показывает все отдельные события, сгруппированные по следующему событию. Например, во второй строке показано, что после события произошло всего 143 событий `Hail` `Tornado` .

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||332|
|2007-01-01 00:00:00.0000000|Град|145|
|2007-01-01 00:00:00.0000000|Шквалистый ветер|143|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|32|
|2007-01-01 00:00:00.0000000|Воронка Cloud|21|
|2007-01-01 00:00:00.0000000|Молнии|4|
|2007-01-01 00:00:00.0000000|Тяжелая дождя|2|
|2007-01-01 00:00:00.0000000|Наводнение|2|
|2007-01-01 00:00:00.0000000|Ураган (Тайфун)|1|

Теперь давайте попробуем узнать, как продолжить выполнение следующей последовательности:  
`Hail` -> `Tornado` -> `Thunderstorm Wind`

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, 
dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']))
```

Пропустив `Table #1` и `Table #2` просматривая, `Table #3` мы можем `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` завершить эту последовательность в 92 событиях, завершенных этой последовательностью, продолжить, как `Hail` и в 41, и вернуться к `Tornado` 14.

|`StartTime`|`next`|`dcount`|
|---------|-----|------|
|2007-01-01 00:00:00.0000000||92|
|2007-01-01 00:00:00.0000000|Град|41|
|2007-01-01 00:00:00.0000000|Торнадо|14|
|2007-01-01 00:00:00.0000000|Внезапное наводнение|11|
|2007-01-01 00:00:00.0000000|Молнии|2|
|2007-01-01 00:00:00.0000000|Тяжелая дождя|1|
|2007-01-01 00:00:00.0000000|Наводнение|1|
