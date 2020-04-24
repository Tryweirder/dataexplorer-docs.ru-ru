---
title: dayofmonth() - Azure Data Explorer Документы Майкрософт
description: Эта статья описывает дневной месяц () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 791d85d4a8f89487e65ef68ecc605f907e63e1ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516343"
---
# <a name="dayofmonth"></a>dayofmonth()

Возвращает целый номер, представляющий дневной номер данного месяца

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

**Синтаксис**

`dayofmonth(`*a_date*`)`

**Аргументы**

* `a_date` — значение в формате `datetime`.

**Возвращает**

`day number`данного месяца.