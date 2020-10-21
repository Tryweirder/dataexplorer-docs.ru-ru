---
title: DayOfWeek () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается DayOfWeek () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b13b7e4f0425cd83aefb41a94b76eb08db27498
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247671"
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

## <a name="returns"></a>Результаты

Интервал `timespan` от полуночи предыдущего воскресенья с округлением до целого числа (количество дней).

## <a name="examples"></a>Примеры

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
