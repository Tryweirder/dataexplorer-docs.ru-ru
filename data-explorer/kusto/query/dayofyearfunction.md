---
title: DayOfYear () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается DayOfYear () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2ac301365cb22849dc07ea137756f4093bea79b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252387"
---
# <a name="dayofyear"></a>dayofyear()

Возвращает целое число, представляющее номер дня данного года.

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>Синтаксис

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>Аргументы

* `a_date` — значение в формате `datetime`.

## <a name="returns"></a>Результаты

`day number` указанного года.