---
title: тоскалар () — Azure обозреватель данных
description: В этой статье описывается тоскалар () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 15d9056ec21eb6f25ccbc985d659f310d670f02d
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133419"
---
# <a name="toscalar"></a>toscalar()

Возвращает скалярное константное значение вычисленного выражения. 

Эта функция полезна для запросов, требующих промежуточных вычислений. Например, вычислите общее число событий, а затем используйте результат для фильтрации групп, превышающих определенный процент всех событий.

**Синтаксис**

`toscalar(`*Выражение*`)`

**Аргументы**

* *Выражение*: выражение, которое будет вычисляться для скалярного преобразования.

**Возвращает**

Скалярное константное значение вычисленного выражения.
Если результат является табличным, то для преобразования будет сделан первый столбец и первая строка.

> [!TIP]
> Можно использовать [инструкцию Let](letstatement.md) для удобочитаемости запроса при использовании `toscalar()` .

**Примечания**

`toscalar()`можно вычислить как постоянное число раз во время выполнения запроса.
`toscalar()`Функция не может быть применена к сценарию на уровне строк (для каждой строки).

**Примеры**

Оцените `Start` , `End` и `Step` в качестве скалярных констант и используйте результат для `range` вычисления.

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|з|start|end|Шаг|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|
