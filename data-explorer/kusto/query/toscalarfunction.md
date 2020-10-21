---
title: тоскалар () — Azure обозреватель данных
description: В этой статье описывается тоскалар () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1210a820b4b3c8790d218ba53992da0255028de2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252022"
---
# <a name="toscalar"></a>toscalar()

Возвращает скалярное константное значение вычисленного выражения. 

Эта функция полезна для запросов, требующих промежуточных вычислений. Например, вычислите общее число событий, а затем используйте результат для фильтрации групп, превышающих определенный процент всех событий.

## <a name="syntax"></a>Синтаксис

`toscalar(`*Выражение*`)`

## <a name="arguments"></a>Аргументы

* *Выражение*: выражение, которое будет вычисляться для скалярного преобразования.

## <a name="returns"></a>Результаты

Скалярное константное значение вычисленного выражения.
Если результат является табличным, то для преобразования будет сделан первый столбец и первая строка.

> [!TIP]
> Можно использовать [инструкцию Let](letstatement.md) для удобочитаемости запроса при использовании `toscalar()` .

**Примечания**

`toscalar()` можно вычислить как постоянное число раз во время выполнения запроса.
`toscalar()`Функция не может быть применена к сценарию на уровне строк (для каждой строки).

## <a name="examples"></a>Примеры

Оцените `Start` , `End` и `Step` в качестве скалярных констант и используйте результат для `range` вычисления.

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|з|запуск|end|Шаг|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|
