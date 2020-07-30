---
title: DayOfMonth () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается DayOfMonth () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3341f416642b06d899c2a3d1f6675f4d3254291f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348502"
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

## <a name="returns"></a>Возвращаемое значение

`day number`заданного месяца.