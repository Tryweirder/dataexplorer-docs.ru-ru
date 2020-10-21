---
title: Оператор notbetween в Azure обозреватель данных
description: В этой статье описывается оператор notbetween в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7dd8f04010cbc10afa34fa8b27e6be8d31feae9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248697"
---
# <a name="not-between-operator-between"></a>Оператор not-between (!between)

Соответствует входным данным, находящимся за пределами включающего диапазона.

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between` может выполнять операции с любым числовым выражением, типом DateTime или значением типа TimeSpan.
 
## <a name="syntax"></a>Синтаксис

*T* `|` `where` *expr* `!between` `(` *лефтранже* ` .. ` *ригхтранже*`)`   
 
Если выражение *expr* имеет тип DateTime, предоставляется еще один синтаксический синтаксис SugarCRM:

*T* `|` `where` *expr* `!between` `(` *лефтранжедатетиме* ` .. ` *ригхтранжетимеспан*`)`   

## <a name="arguments"></a>Аргументы

* *T* — табличный ввод, записи которого должны быть сопоставлены.
* *expr* — выражение для фильтрации.
* *лефтранже* -выражение левого диапазона (включительно).
* *ригхтранже* -выражение правого диапазона (включительно).

## <a name="returns"></a>Результаты

Строки в *T* , для которых предикат (*expr*  <  *лефтранже* или *expr*  >  *ригхтранже*) принимает значение `true` .

## <a name="examples"></a>Примеры  

**Фильтрация числовых значений с помощью оператора "! Between"**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**Фильтрация даты и времени с помощью оператора "Between"**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Счетчик|
|---|
|58590|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Счетчик|
|---|
|58590|
