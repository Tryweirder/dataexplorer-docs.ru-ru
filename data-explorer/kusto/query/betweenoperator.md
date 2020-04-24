---
title: между оператором - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается между оператором в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 336d24edbcd7574f0c4b6375b4b09014b38d10ec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517856"
---
# <a name="between-operator"></a>Оператор between

проверяет, чтобы входные данные находились внутри указанного диапазона.

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between`может работать на любом численном, времени даты или выражении тайм-span.
 
**Синтаксис**

*T* `|` T `where` *expr* `between` *leftRange*` .. `*rightRange* leftRange rightRange `(``)`   
 
Если *expr* выражение дата - другой синтаксической синтаксис сахара синтаксис предоставляется:

*T* `|` T `where` *expr* `between` *leftRangeDateTime*` .. `*rightRangeTimespan* leftRangeDateTime rightRangeTimespan `(``)`   

**Аргументы**

* *T* - Табулярный вход, записи которого должны быть сопоставлены.
* *expr* - выражение для фильтрации.
* *leftRange* - выражение левого диапазона (включительно).
* *rightRange* - выражение правильного диапазона (включительно).

**Возвращает**

Строки в *T,* для которых предикат *(expr* >= *leftRange* и `true` *expr* <= *rightRange*) оценивается в .

**Примеры**  

**Фильтрация числовых значений с помощью оператора «между»**  

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

**Время фильтрации даты с помощью оператора «между»**  


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|476|


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|476|