---
title: bin_auto () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны bin_auto () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebb214ae6a2676bf59a37e1e4e9cc3c085374bb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517839"
---
# <a name="bin_auto"></a>bin_auto()

Раунды значения вплоть до "бин" фиксированного размера, с контролем над размером ячейки и отправной точкой, обеспечиваемыми свойством запроса.

**Синтаксис**

`bin_auto``(` *Выражение*`)`

**Аргументы**

* *Выражение*: масштабируемое выражение численного типа с указанием значения для раунда.

**Свойства запроса клиента**

* `query_bin_auto_size`: Числовой буквальный с указанием размера каждого бункера.
* `query_bin_auto_at`: Числовой буквальный указывая одно значение *выражения,* которое является `fixed_point` "фиксированной точкой" (т.е. значение, для которого `bin_auto(fixed_point)` == `fixed_point`.)

**Возвращает**

Ближайший кратный `query_bin_auto_at` ниже *выражение*, `query_bin_auto_at` сдвинуты так, что будет переведен о собой.

**Примеры**

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Отметка времени                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |