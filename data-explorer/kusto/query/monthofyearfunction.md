---
title: monthofyear() - Исследователь данных Azure (ru) Документы Майкрософт
description: Эта статья описывает monthofyear () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ada34d3e6f6c905a1acfb550b02af3dab550fb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512348"
---
# <a name="monthofyear"></a>monthofyear()

Возвращает целый номер представляет номер месяца данного года.

Другой псевдоним: getmonth()

```kusto
monthofyear(datetime("2015-12-14"))
```

**Синтаксис**

`monthofyear(`*a_date*`)`

**Аргументы**

* `a_date` — значение в формате `datetime`.

**Возвращает**

`month number`данного года.