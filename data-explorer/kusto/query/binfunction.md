---
title: bin () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается bin () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9bafeee9cec5ac81034b879f054e445d8b118dcf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243410"
---
# <a name="bin"></a>bin()

Округляет значения до целого, кратного указанному размеру группы. 

Часто используется в сочетании с [`summarize by ...`](./summarizeoperator.md) .
При наличии разрозненного набора значений они будут сгруппированы в меньший набор определенных значений.

Значения NULL, размер ячейки null или отрицательный размер ячейки приводят к значению NULL. 

Псевдоним для `floor()` функции.

## <a name="syntax"></a>Синтаксис

`bin(`*значение* `,` *roundTo*`)`

## <a name="arguments"></a>Аргументы

* *value*: число, дата или интервал времени. 
* *roundTo*: "размер ячейки". Число или интервал времени, разделяющий *значение*. 

## <a name="returns"></a>Результаты

Ближайшее число, кратное *roundTo* и меньшее *value*.  
 
```kusto
(toint((value/roundTo))) * roundTo`
```

## <a name="examples"></a>Примеры

Выражение | Результат
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1970-05-11 13:45:07), 1d)`|  `datetime(1970-05-11)`


Следующее выражение вычисляет гистограмму длительности с размером сегмента в 1 секунду:

```kusto
T | summarize Hits=count() by bin(Duration, 1s)
```
