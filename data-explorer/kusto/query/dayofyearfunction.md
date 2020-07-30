---
title: DayOfYear () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается DayOfYear () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 925b65846c6ba32163bd325fd2ee3321bc7fc802
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348468"
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

`day number`указанного года.