---
title: session_count плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается session_count плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 76ce6ca3be1859aba0a94ae155c60342be3f1ad1
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663151"
---
# <a name="session_count-plugin"></a>Подключаемый модуль session_count

Вычисления сеансов учитываются на основе столбца идентификатора в течение временной шкалы.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**Синтаксис**

*T* `| evaluate` `,` *End* `,` *Bin* `,` `,` `,` `,` *IdColumn* `,` `,` *Start* *dim2* *LookBackWindow* *dim1* *TimelineColumn* IdColumn СрокиКолонка Начало Конец Бин LookBackWindow - dim1 dim2 ..." `session_count(``)`

**Аргументы**

* *T*: Входное табеляцивское выражение.
* *IdColumn*: Название столбца с значениями идентификатора, которые представляют активность пользователя. 
* *TimelineColumn*: Название столбца, представляющего временную шкалу.
* *Начало*: Scalar со значением периода начала анализа.
* *Конец*: Scalar со значением конечного периода анализа.
* *Бин*: масштабировать постоянное значение периода шага анализа сеанса.
* *LookBackWindow*: масштабирование постоянного значения, представляющего период возврата сессии. Если идентификатор от `IdColumn` `LookBackWindow` появляется в временном окне внутри - сеанс считается существующим, если он не является - сеанс считается новым.
* *dim1*, *dim2*, ...: (необязательный) список столбцов размеров, которые нарезает расчет количества сеансов.

**Возвращает**

Возвращает таблицу с значениями количества сеансов для каждого периода временной шкалы и для каждой существующей комбинации измерений.

Схема таблицы вывода:

|*ХронологияКоля*|dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|тип: по состоянию на *ХроникаColumn*|..|..|..|long|


**Примеры**


Ради примера мы сделаем данные детерминированными - таблицу с двумя столбцов:
- Хронология: бегущий номер от 1 до 10 000
- Идентификатор: Идентификатор пользователя от 1 до 50

`Id`отображаются в конкретном `Timeline` слоте, если это разделитель `Timeline` (Timeline % Id no 0).

Это означает, `Id==1` что событие `Timeline` с появится в `Id==2` любом `Timeline` слоте, событие с на каждом втором слоте, и так далее.

Вот несколько 20 строк данных:

```kusto
let _data = range Timeline from 1 to 10000 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// Look on few lines of the data
_data
| order by Timeline asc, Id asc
| limit 20
```

|Временная шкала|Идентификатор|
|---|---|
|1|1|
|2|1|
|2|2|
|3|1|
|3|3|
|4|1|
|4|2|
|4|4|
|5|1|
|5|5|
|6|1|
|6|2|
|6|3|
|6|6|
|7|1|
|7|7|
|8|1|
|8|2|
|8|4|
|8|8|

Давайте определим сеанс в следующих терминах: сеанс считается`Id`активным до тех пор, пока пользователь () появляется по крайней мере один раз в 100 временных интервалах, в то время как окно возврата времени сеанса составляет 41 временной интервал.

Следующий запрос показывает количество активных сеансов в соответствии с приведенным выше определением.

```kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart 
```

:::image type="content" source="images/queries/example-session-count.png" alt-text="Количество примеров сеансов":::
