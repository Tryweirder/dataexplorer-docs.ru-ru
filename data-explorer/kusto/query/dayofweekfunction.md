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
ms.openlocfilehash: 04b6122c7517d79d5563892a621eed8cde3b948a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348485"
---
# <a name="dayofweek"></a>dayofweek()

Возвращает целое число дней с момента предыдущего воскресенья в виде `timespan` .

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

## <a name="syntax"></a>Синтаксис

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>Аргументы

* `a_date` — значение в формате `datetime`.

## <a name="returns"></a>Возвращаемое значение

Интервал `timespan` от полуночи предыдущего воскресенья с округлением до целого числа (количество дней).

## <a name="examples"></a>Примеры

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
