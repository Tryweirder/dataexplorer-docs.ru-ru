---
title: в и notin операторов - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описаны операторы в Azure Data Explorer и не в ней.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: bd247de2bd211ae7be3da449e940899d2e8bb475
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513810"
---
# <a name="in-and-in-operators"></a>Операторы in и !in

Фильтрует набор записей, основанный на предоставленном наборе значений.

```kusto
Table1 | where col in ('value1', 'value2')
```

**Синтаксис**

*Дело чувствительных синтаксис:*

*T* `|` T `where` *col* `in` col `(` *список скалярных выражений*`)`   
*T* `|` `where` *col* col `in` табулярное *выражение* `(``)`   
 
*T* `|` T `where` *col* `!in` col `(` *список скалярных выражений*`)`  
*T* `|` `where` *col* col `!in` табулярное *выражение* `(``)`   

*Дело бесчувственный синтаксис:*

*T* `|` T `where` *col* `in~` col `(` *список скалярных выражений*`)`   
*T* `|` `where` *col* col `in~` табулярное *выражение* `(``)`   
 
*T* `|` T `where` *col* `!in~` col `(` *список скалярных выражений*`)`  
*T* `|` `where` *col* col `!in~` табулярное *выражение* `(``)`   

**Аргументы**

* *T* - Табулярный ввод, записи которого должны быть отфильтротаны.
* *col* - столбец для фильтрации.
* *список выражений* - запятая разделенный список табликовых, скалярных или буквальных выражений  
* *таблоское выражение* - табло-выражение, имевававеское набор значений (в случае выражение имеет несколько столбцов, используется первая колонка)

**Возвращает**

Строки в *T,* для которых предикат`true`

**Примечания**

* Список выражений может `1,000,000` создавать до значений    
* Вложенные массивы сплющиваются в единый список `x in (dynamic([1,[2,3]]))` значений, например, превращаются в`x in (1,2,3)` 
* В случае табликовых выражений выбирается первый столбец набора результатов   
* Добавление 'к оператору' делает поисковик `x in~ (expression)` `x !in~ (expression)`значений нечувствительным: или .

**Примеры:**  

**Простое использование оператора 'in':**  

```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|4775|  


**Простое использование оператора 'in':**  

```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Count|
|---|
|4775|  

**Простое использование оператора '!in':**  

```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|54291|  


**Использование динамического массива:**
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Count|
|---|
|3218|


**Пример подвоза:**  

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

Тот же запрос может быть написан как:

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

**Топ с другим примером:**  

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
| Висконсин | 31                   |
| Техас     | 55                   |
| Флорида   | 85                   |
| Грузия   | 106                  |
| Другой     | 415                  |

**Использование статического списка, возвращенного функцией:**  

```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Count|
|---|
|4775|  


Вот определение функции:  

```kusto
.show function InterestingStates
```

|Имя|Параметры|Текст|Папка|DocString|
|---|---|---|---|---|
|Интересныегосударства|()|- динамический ("ВАШИНГТОН", "ФЛОРИДА", "Грузия", "НЬЮ-йорк")
