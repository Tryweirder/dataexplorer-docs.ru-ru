---
title: DayOfMonth () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается DayOfMonth () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e98e1405a6521ef9cdaf40a24b2c173bdf72c376
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252401"
---
# <a name="dayofmonth"></a>dayofmonth()

Возвращает целое число, представляющее номер дня данного месяца

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

## <a name="syntax"></a>Синтаксис

`dayofmonth(`*a_date*`)`

## <a name="arguments"></a>Аргументы

* `a_date` — значение в формате `datetime`.

## <a name="returns"></a>Результаты

`day number` заданного месяца.