---
title: has_anyный оператор обозреватель данных Azure
description: В этой статье описывается оператор has_any в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 4485dde5eb77478e5fd75ce388ada7f4232f2ddb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347635"
---
# <a name="has_any-operator"></a>Оператор has_any

`has_any`Фильтры операторов на основе предоставленного набора значений.

```kusto
Table1 | where col has_any ('value1', 'value2')
```

## <a name="syntax"></a>Синтаксис

*T* `|` `where` *Col* `has_any` `(` *список скалярных выражений*`)`   
*T* `|` `where` *col* `has_any` `(` *Табличное выражение* T Col`)`   
 
## <a name="arguments"></a>Аргументы

* *T* -табличный ввод, записи которого должны быть отфильтрованы.
* *столбец для* фильтрации.
* *список выражений* — список табличных, скалярных и литеральных выражений с разделителями-запятыми  
* *табличное выражение* — табличное выражение, имеющее набор значений (если выражение содержит несколько столбцов, используется первый столбец)

## <a name="returns"></a>Результаты

Строки в *T* , для которых предикатом является`true`

**Примечания**

* Список выражений может выдавать до `10,000` значений.    
* Для табличных выражений выбирается первый столбец результирующего набора.   

**Примеры:**  

**Простое использование `has_any` оператора:**  

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where State has_any ("CAROLINA", "DAKOTA", "NEW") 
| summarize count() by State
```

|Состояние|count_|
|---|---|
|НЬЮ-ЙОРК|1750|
|СЕВЕРНАЯ КАРОЛИНА|1721|
|ЮЖНАЯ ДАКОТА|1567|
|НЬЮ ДЖЕРСИ|1044|
|ЮЖНАЯ КАРОЛИНА|915|
|СЕВЕРНАЯ ДАКОТА|905|
|НОВЫЙ МЕКСИКА|527|
|СОЗДАТЬ НЬЮ ГЕМПШИР|394|


**Использование динамического массива:**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let states = dynamic(['south', 'north']);
StormEvents 
| where State has_any (states)
| summarize count() by State
```

|Состояние|count_|
|---|---|
|СЕВЕРНАЯ КАРОЛИНА|1721|
|ЮЖНАЯ ДАКОТА|1567|
|ЮЖНАЯ КАРОЛИНА|915|
|СЕВЕРНАЯ ДАКОТА|905|
|АТЛАНТИЧЕСКОЕ ЮГО|193|
|АТЛАНТИЧЕСКОЕ ПОБЕРЕЖЬЕ|188|
