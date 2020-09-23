---
title: series_equals () — обозреватель данных Azure
description: В этой статье описывается series_equals () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 55da6ece7e99537b4107c3ae03c428c8e234d1bf
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103612"
---
# <a name="series_equals"></a>series_equals()

Вычисляет логику поэлементного равенства ( `==` ) двух входных числовых рядов.

## <a name="syntax"></a>Синтаксис

`series_equals (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Аргументы

* *Series1, Series2*. входные числовые массивы для поэлементного сравнения. Все аргументы должны быть динамическими массивами. 

## <a name="returns"></a>Результаты

Динамический массив логических значений, содержащий вычисляемую поэлементную операцию равенства между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_equals_s2 = series_equals(s1, s2)
```

|s1|s2|s1_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, true, false]|

## <a name="see-also"></a>См. также раздел

Сравнение статистики по всем рядам см. в следующих статьях:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
