---
title: bin() - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается бен() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3fb827c71fa63fde031a91bc9aec7f0ed108fd5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517431"
---
# <a name="bin"></a>bin()

Округляет значения до целого, кратного указанному размеру группы. 

Часто используется в [`summarize by ...`](./summarizeoperator.md)сочетании с .
При наличии разрозненного набора значений они будут сгруппированы в меньший набор определенных значений.

Нулевые значения, размер нулевой ячейки или отрицательный размер янина приведут к нулю. 

Прозвище `floor()` для функционирования.

**Синтаксис**

`bin(`*значение*`,`*roundTo*`)`

**Аргументы**

* *значение*: число, дата или временной промежуток. 
* *roundTo*: "размер бин". Число, дата или временной диапазон, на который делится *value*. 

**Возвращает**

Ближайшее число, кратное *roundTo* и меньшее *value*.  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

**Примеры**

Выражение | Результат
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


Следующее выражение вычисляет гистограмму длительности с размером сегмента в 1 секунду:

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```