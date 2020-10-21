---
title: series_add () — обозреватель данных Azure
description: В этой статье описывается series_add () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a47916d13318c4af1800fabff88d815d4e3e23cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250167"
---
# <a name="series_add"></a>series_add()

Вычисляет Поэлементное добавление двух входных числовых рядов.

## <a name="syntax"></a>Синтаксис

`series_add(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Аргументы

* *Series1, Series2*: входные числовые массивы, которые должны быть поэлементно добавлены в результат динамического массива. Все аргументы должны быть динамическими массивами. 

## <a name="returns"></a>Результаты

Динамический массив вычисляемого элемента операции добавления на основе двух входных значений. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[5, 4, 5]|
|[2, 4, 8]|[8, 4, 2]|[10, 8, 10]|
|[3, 6, 12]|[12, 6, 3]|[15, 12, 15]|
