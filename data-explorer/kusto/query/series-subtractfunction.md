---
title: series_subtract () — обозреватель данных Azure
description: В этой статье описывается series_subtract () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9276b3d920259b4a73d3fb45c056837439b43fba
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351239"
---
# <a name="series_subtract"></a>series_subtract()

Вычисляет поэлементное вычитание двух входных числовых рядов.

## <a name="syntax"></a>Синтаксис

`series_subtract(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Аргументы

* *Series1, Series2*: входные числовые массивы, второй — поэлементное вычитание из первого в результат динамического массива. Все аргументы должны быть динамическими массивами. 

## <a name="returns"></a>Возвращаемое значение

Динамический массив вычисляемой операции вычитания по элементам между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[-3, 0, 3]|
|[2, 4, 8]|[8, 4, 2]|[-6, 0, 6]|
|[3, 6, 12]|[12, 6, 3]|[-9, 0, 9]|
