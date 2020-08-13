---
title: bin () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается bin () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb6c7c51e295f9af9d6e43a5de5936dfea13f5b6
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201664"
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

## <a name="returns"></a>Возвращаемое значение

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
