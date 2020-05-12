---
title: оператор Between в Azure обозреватель данных
description: В этой статье описывается оператор Between в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ef64818c9c5e345ffb60999c97273670026be022
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227627"
---
# <a name="between-operator"></a>Оператор between

проверяет, чтобы входные данные находились внутри указанного диапазона.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between`может выполнять операции с любым числовым выражением, типом DateTime или значением типа TimeSpan.
 
**Синтаксис**

*T* `|` `where` *expr* `between` `(` *лефтранже* ` .. ` *ригхтранже*`)`   
 
Если выражение *expr* имеет тип DateTime, предоставляется еще один синтаксический синтаксис SugarCRM:

*T* `|` `where` *expr* `between` `(` *лефтранжедатетиме* ` .. ` *ригхтранжетимеспан*`)`   

**Аргументы**

* *T* — табличный ввод, записи которого должны быть сопоставлены.
* *expr* — выражение для фильтрации.
* *лефтранже* -выражение левого диапазона (включительно).
* *ригхтранже* -выражение правого диапазона (включительно).

**Возвращает**

Строки в *T* , для которых предикат (*expr*  >=  *лефтранже* и *expr*  <=  *ригхтранже*) принимает значение `true` .

**Примеры**  

**Фильтрация числовых значений с помощью оператора "Between"**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1
| where x between (50 .. 55)
```

|x|
|---|
|50|
|51|
|52|
|53|
|54|
|55|

**Фильтрация даты и времени с помощью оператора "Between"**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Счетчик|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Счетчик|
|---|
|476|
