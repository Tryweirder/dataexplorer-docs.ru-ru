---
title: DayOfWeek () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается DayOfWeek () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e445a86b976f251de2beef4726c4840bcec8e44
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722037"
---
# <a name="dayofweek"></a>dayofweek()

Возвращает целое число дней с момента предыдущего воскресенья в виде `timespan` .

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

**Синтаксис**

`dayofweek(`*a_date*`)`

**Аргументы**

* `a_date` — значение в формате `datetime`.

**Возвращает**

Интервал `timespan` от полуночи предыдущего воскресенья с округлением до целого числа (количество дней).

**Примеры**

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
