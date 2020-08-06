---
title: операторы in и notin — Azure обозреватель данных
description: В этой статье описываются операторы и notin в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: a6551ee2d4ac01d6d896cc8daff466f3c4a7852e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803970"
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

|Count|
|---|
|4775|  

### <a name="use-in-operator"></a>Использовать оператор "in ~"  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Count|
|---|
|4775|  

### <a name="use-in-operator"></a>Использовать оператор "! in"

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
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

|Count|
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

|Count|
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
| Другое     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>Использовать статический список, возвращенный функцией

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Count|
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
