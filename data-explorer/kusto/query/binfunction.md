---
title: bin() в Azure Data Explorer | Документация Майкрософт
description: В этой статье описана функция bin() в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
adobe-target: true
ms.openlocfilehash: edca199ed2ba12fa74e69c66f1b1396313606256
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359902"
---
# <a name="bin"></a>bin()

Округляет значения до целого, кратного указанному размеру группы. 

Часто используется в сочетании с [`summarize by ...`](./summarizeoperator.md).
При наличии разрозненного набора значений они будут сгруппированы в меньший набор определенных значений.

При указании значения NULL, размера ячейки NULL или отрицательного значения размера ячейки возвращается значение NULL. 

Псевдоним для функции `floor()`.

## <a name="syntax"></a>Синтаксис

`bin(`*value*`,`*roundTo*`)`

## <a name="arguments"></a>Аргументы

* *value*: число, дата или [временной диапазон](scalar-data-types/timespan.md). 
* *roundTo*: значение "Размер ячейки". Число или временной диапазон, на который делится *value*. 

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
