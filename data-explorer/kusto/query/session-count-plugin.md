---
title: подключаемый модуль session_count — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль session_count в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1e173dcba48e8748562bad61e0f16786e957ca83
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550560"
---
# <a name="session_count-plugin"></a>Подключаемый модуль session_count

Вычисляет число сеансов на основе столбца ИДЕНТИФИКАТОРов на временной шкале.

```kusto
T | evaluate session_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 1min, 30min, dim1, dim2, dim3)
```

**Синтаксис**

*T* `| evaluate` `session_count(` *идколумн* `,` *тимелинеколумн* `,` *Start* `,` *End* `,` *Bin* `,` *лукбакквиндов* [ `,` *Dim1* `,` *dim2* `,` ...]`)`

**Аргументы**

* *T*: Входное табличное выражение.
* *Идколумн*: имя столбца со значениями идентификаторов, представляющими действия пользователя. 
* *Тимелинеколумн*: имя столбца, представляющего временную шкалу.
* *Начало*: скалярное значение начального периода анализа.
* *End*: скаляр со значением периода окончания анализа.
* *Bin*: скалярное постоянное значение периода анализа сеанса.
* *Лукбакквиндов*: скалярное постоянное значение, представляющее период лукбакк сеанса. Если идентификатор из `IdColumn` отображается в окне времени в `LookBackWindow` , то сеанс считается существующим. Если идентификатор не отображается, то сеанс считается новым.
* *Dim1*, *dim2*,...: (необязательно) список столбцов измерений, которые срезируют вычисление количества сеансов.

**Возвращает**

Возвращает таблицу со значениями счетчика сеансов для каждого периода временной шкалы и для каждого сочетания существующих измерений.

Схема выходной таблицы:

|*тимелинеколумн*|Dim1|..|dim_n|count_sessions|
|---|---|---|---|---|--|--|--|--|--|--|
|Тип: от *тимелинеколумн*|..|..|..|long|


**Примеры**

В этом примере данные детерминированы, и мы используем таблицу с двумя столбцами:
- Временная шкала: число с интервалом от 1 до 10 000
- Идентификатор: идентификатор пользователя от 1 до 50.

`Id`отображается в определенном `Timeline` слоте, если он является разделителем `Timeline` (временная шкала% ID = = 0).

Событие с `Id==1` будет отображаться в любом `Timeline` слоте, событии со `Id==2` вторым `Timeline` слотом и т. д.

Ниже приведено несколько 20 строк данных:

<!-- csl: https://help.kusto.windows.net/Samples -->
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

Давайте определим сеанс в следующих терминах: сеанс считается активным, пока пользователь ( `Id` ) отображается по крайней мере один раз в интервале времени 100, а окно просмотра сеанса — 41.

Следующий запрос показывает количество активных сеансов в соответствии с указанным выше определением.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

:::image type="content" source="images/session-count-plugin/example-session-count.png" alt-text="Пример числа сеансов" border="false":::
