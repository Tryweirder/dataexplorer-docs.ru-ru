---
title: series_pearson_correlation () — обозреватель данных Azure
description: В этой статье описывается series_pearson_correlation () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 3b65dff40e644852555465fe6ce07ed94c4920ea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351324"
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
