---
title: toscalar () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описан toscalar() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 60fe8123760a9921bfa7abfacbbdffba6dba8d7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505905"
---
# <a name="toscalar"></a>toscalar()

Возвращает скаларное постоянное значение оцененного выражения. 

Эта функция полезна для запросов, требующих поэтапных вычислений, например для расчета общего количества событий, а затем для фильтрации групп, превышающих определенный процент всех событий. 

**Синтаксис**

`toscalar(`*Выражение*`)`

**Аргументы**

* *Выражение*: Выражение, которое будет оценено для конверсии scalar  

**Возвращает**

Постоянное значение оцененного выражения.
Если результат выражения является табликским, то первый столбец и первый ряд будут приняты для преобразования.

> [!TIP]
> Вы можете использовать [заявление let](letstatement.md) для читаемости `toscalar()`запроса при использовании.

**Примечания**

`toscalar()`можно вычислить постоянное количество раз во время выполнения запроса.
Другими словами, `toscalar()` функция не может быть применена на уровне строки (для каждого ряда сценария).

**Примеры**

Следующий запрос `Start`оценивает, `End` `Step` и как константы скалара `range` - и использовать его для оценки. 

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|start|end|Шаг|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|