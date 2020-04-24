---
title: series_pearson_correlation() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_pearson_correlation () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 6454ec528e7a9e53b2feab5a7fefa1236ed80cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508115"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

Высчитывает коэффициент корреляции пирсона из двух входов численной серии.

См.: [Коэффициент корреляции Пирсона](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient).

**Синтаксис**

`series_pearson_correlation(`*Серия1* `,` *Серия2*`)`

**Аргументы**

* *Серия1, Серия2*: Числовые массивы для расчета коэффициента корреляции. Все аргументы должны быть динамическими массивами одинаковой длины. 

**Возвращает**

Рассчитанный коэффициент корреляции Пирсона между двумя входными путами. Результат дает любой `null` нечислоный элемент или несуществующий элемент (массивы разных размеров).

**Пример**

```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1,2,3,4,5]|[2,4,6,8,10]|1|
