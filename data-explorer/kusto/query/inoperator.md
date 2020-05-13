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
ms.openlocfilehash: cd11362c15e5ecfb80eab57b57b22f190f47da05
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271576"
---
# <a name="in-and-in-operators"></a>Операторы in и !in

Фильтрует набор записей на основе предоставленного набора значений.

```kusto
Table1 | where col in ('value1', 'value2')
```

**Синтаксис**

*Синтаксис с учетом регистра:*

*T* `|` `where` *Col* `in` `(` *список скалярных выражений*`)`   
*T* `|` `where` *col* `in` `(` *Табличное выражение* T Col`)`   
 
*T* `|` `where` *Col* `!in` `(` *список скалярных выражений*`)`  
*T* `|` `where` *col* `!in` `(` *Табличное выражение* T Col`)`   

*Синтаксис без учета регистра:*

*T* `|` `where` *Col* `in~` `(` *список скалярных выражений*`)`   
*T* `|` `where` *col* `in~` `(` *Табличное выражение* T Col`)`   
 
*T* `|` `where` *Col* `!in~` `(` *список скалярных выражений*`)`  
*T* `|` `where` *col* `!in~` `(` *Табличное выражение* T Col`)`   

**Аргументы**

* *T* — табличный ввод, записи которого должны быть отфильтрованы.
* *Col* — столбец для фильтрации.
* *список выражений* — разделенный запятыми список табличных, скалярных или литеральных выражений  
* *табличное выражение* — табличное выражение, имеющее набор значений (в выражении CASE есть несколько столбцов, используется первый столбец).

**Возвращает**

Строки в *T* , для которых предикатом является`true`

**Примечания**

* Список выражений может выдавать до `1,000,000` значений    
* Вложенные массивы сведены в один список значений, например `x in (dynamic([1,[2,3]]))` превращается в`x in (1,2,3)` 
* В случае табличных выражений выбирается первый столбец результирующего набора   
* При добавлении "~" к оператору значения "без учета регистра" не учитываются: `x in~ (expression)` или `x !in~ (expression)` .

**Примеры:**  

**Простое использование оператора in:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Счетчик|
|---|
|4775|  


**Простое использование оператора "in ~":**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Счетчик|
|---|
|4775|  

**Простое использование оператора "! in":**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Счетчик|
|---|
|54291|  


**Использование динамического массива:**

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


**Пример вложенного запроса:**  

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

**Top с другим примером:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| State     | sum_lightning_events |
|-----------|----------------------|
| АЛАБАМА   | 29                   |
| ВИСКОНСИНА | 31                   |
| Техас     | 55                   |
| Флорида   | 85                   |
| Грузия   | 106                  |
| другой     | 415                  |

**Использование статического списка, возвращаемого функцией:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Счетчик|
|---|
|4775|  


Ниже приведено определение функции.  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|Имя|Параметры|Текст|Папка|DocString|
|---|---|---|---|---|
|интерестингстатес|()|{Dynamic (["Вашингтон", "ФЛОРИДЕ", "Грузия", "Нью-Йорк"])}
