---
title: series_pearson_correlation () — обозреватель данных Azure
description: В этой статье описывается series_pearson_correlation () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: aa75e3cb2f2aefbc50c148486cb687841408e1d4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245969"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Вычисляет коэффициент корреляции Пирсона двух входных числовых рядов.

См. раздел [коэффициент корреляции Пирсона](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

## <a name="syntax"></a>Синтаксис

`series_pearson_correlation(`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Аргументы

* *Series1, Series2*: входные числовые массивы для вычисления коэффициента корреляции. Все аргументы должны быть динамическими массивами одинаковой длины. 

## <a name="returns"></a>Результаты

Вычисленный коэффициент корреляции Пирсона между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` результат.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1, 2, 3, 4, 5]|[2, 4, 6, 8, 10]|1|
