---
title: notbetween оператора - Azure Data Explorer Документы Майкрософт
description: В этой статье описано немежду оператором в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: eacde679f05ff79f5ee0d223ba005217dbf5192c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512093"
---
# <a name="between-operator"></a>!между оператором

Соответствует входу, который находится за пределами инклюзивного диапазона.

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between`может работать на любом численном, времени даты или выражении тайм-span.
 
**Синтаксис**

*T* `|` T `where` *expr* `!between` *leftRange*` .. `*rightRange* leftRange rightRange `(``)`   
 
Если *expr* выражение дата - другой синтаксической синтаксис сахара синтаксис предоставляется:

*T* `|` T `where` *expr* `!between` *leftRangeDateTime*` .. `*rightRangeTimespan* leftRangeDateTime rightRangeTimespan `(``)`   

**Аргументы**

* *T* - Табулярный вход, записи которого должны быть сопоставлены.
* *expr* - выражение для фильтрации.
* *leftRange* - выражение левого диапазона (включительно).
* *rightRange* - выражение диапазона rihgt (включительно).

**Возвращает**

Строки в *T,* для которых предикат *(expr* < *leftRange* или `true` *expr* > *rightRange*) оценивается в .

**Примеры**  

**Фильтрация числовых значений с помощью оператора '!between'**  

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

**Время фильтрации даты с помощью оператора «между»**  


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|58590|


```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|58590|