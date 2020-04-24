---
title: dayofyear() - Azure Data Explorer Документы Майкрософт
description: Эта статья описывает dayofyear() в Исследователе данных Лазурных Данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41e7c5906da001e877dd9124124e126d729e886d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516224"
---
# <a name="dayofyear"></a>dayofyear()

Возвращает целый номер представляет дневное число данного года.

```kusto
dayofyear(datetime(2015-12-14))
```

**Синтаксис**

`dayofweek(`*a_date*`)`

**Аргументы**

* `a_date` — значение в формате `datetime`.

**Возвращает**

`day number`данного года.