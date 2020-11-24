---
title: операторы in и notin — Azure обозреватель данных
description: В этой статье описываются операторы и notin в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.localizationpriority: high
ms.openlocfilehash: ffb24abe744bfbe3f7f95336edf0263becfa7ec9
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513256"
---
# <a name="in-and-in-operators"></a>Операторы in и !in

Фильтрует набор записей на основе указанного набора значений.

```kusto
Table1 | where col in ('value1', 'value2')
```

> [!NOTE]
> * Добавление "~" к оператору делает значения "без учета регистра символов" `x in~ (expression)` или `x !in~ (expression)` .
> * В табличных выражениях выбирается первый столбец результирующего набора.
> * Список выражений может выдавать до `1,000,000` значений.
> * Вложенные массивы сведены в один список значений. Например, `x in (dynamic([1,[2,3]]))` преобразуется в `x in (1,2,3)`.
 
## <a name="syntax"></a>Синтаксис

### <a name="case-sensitive-syntax"></a>Синтаксис с учетом регистра

*T* `|` `where` *Col* `in` `(` *список скалярных выражений*`)`   
*T* `|` `where` *col* `in` `(` *Табличное выражение* T Col`)`   
 
*T* `|` `where` *Col* `!in` `(` *список скалярных выражений*`)`  
*T* `|` `where` *col* `!in` `(` *Табличное выражение* T Col`)`   

### <a name="case-insensitive-syntax"></a>Синтаксис без учета регистра

*T* `|` `where` *Col* `in~` `(` *список скалярных выражений*`)`   
*T* `|` `where` *col* `in~` `(` *Табличное выражение* T Col`)`   
 
*T* `|` `where` *Col* `!in~` `(` *список скалярных выражений*`)`  
*T* `|` `where` *col* `!in~` `(` *Табличное выражение* T Col`)`   

## <a name="arguments"></a>Аргументы

* *T* — табличный ввод, записи которого должны быть отфильтрованы.
* *Col* — столбец для фильтрации.
* *список выражений* — разделенный запятыми список табличных, скалярных или литеральных выражений.
* *табличное выражение* — табличное выражение, имеющее набор значений. Если выражение содержит несколько столбцов, используется первый столбец.

## <a name="returns"></a>Возвращаемое значение

Строки в *T* , для которых предикат имеет значение `true` .

## <a name="examples"></a>Примеры  

### <a name="use-in-operator"></a>Использовать оператор "in"

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Счетчик|
|---|
|4775|  

### <a name="use-in-operator"></a>Использовать оператор "in ~"  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Счетчик|
|---|
|4775|  

### <a name="use-in-operator"></a>Использовать оператор "! in"

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Счетчик|
|---|
|54291|  


### <a name="use-dynamic-array"></a>Использовать динамический массив

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Счетчик|
|---|
|3218|

### <a name="subquery"></a>Вложенный запрос

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Using subquery
let Top_5_States = 
StormEvents
| summarize count() by State
| top 5 by count_; 
StormEvents 
| where State in (Top_5_States) 
| count
```

Тот же запрос можно написать следующим образом:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Inline subquery 
StormEvents 
| where State in (
    ( StormEvents
    | summarize count() by State
    | top 5 by count_ )
) 
| count
```

|Счетчик|
|---|
|14242|  

### <a name="top-with-other-example"></a>Top с другим примером

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| Состояние     | sum_lightning_events |
|-----------|----------------------|
| АЛАБАМА   | 29                   |
| ВИСКОНСИНА | 31                   |
| Техас     | 55                   |
| Флорида   | 85                   |
| Грузия   | 106                  |
| Другие     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>Использовать статический список, возвращенный функцией

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Счетчик|
|---|
|4775|  

Определение функции.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|Имя|Параметры|Текст|Папка|DocString|
|---|---|---|---|---|
|интерестингстатес|()|{Dynamic (["Вашингтон", "ФЛОРИДЕ", "Грузия", "Нью-Йорк"])}
