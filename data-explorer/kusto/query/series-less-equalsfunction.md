---
title: series_less_equals () — обозреватель данных Azure
description: В этой статье описывается series_less_equals () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 0373fdf7c687dd7af63895ce3a542ae9b6b4f150
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103535"
---
# <a name="series_less_equals"></a>series_less_equals()

Вычисляет операцию логического элемента "меньше или равно" ( `<=` ) с двумя входными числовыми последовательностями.

## <a name="syntax"></a>Синтаксис

`series_less_equals (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>Аргументы

* *Series1, Series2*. входные числовые массивы для поэлементного сравнения. Все аргументы должны быть динамическими массивами. 

## <a name="returns"></a>Результаты

Динамический массив логических значений, который содержит вычисляемую логическую операцию "меньше или равно" между двумя входными значениями. Любой нечисловой элемент или несуществующий элемент (массивы разного размера) дает `null` значение элемента.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_equals_s2 = series_less_equals(s1, s2)
```

|s1|s2|s1_less_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true, true, false]|

## <a name="see-also"></a>См. также раздел

Сравнение статистики по всем рядам см. в следующих статьях:
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
