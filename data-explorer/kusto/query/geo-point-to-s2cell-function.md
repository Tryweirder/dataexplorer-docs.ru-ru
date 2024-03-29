---
title: geo_point_to_s2cell () — обозреватель данных Azure
description: В этой статье описывается geo_point_to_s2cell () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a094644e6f8a96631e5e1bcf1d2d15cf1ba7caad
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347754"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

Вычисляет строковое значение токена ячейки S2 для географического расположения.

Узнайте больше о [иерархии ячеек S2](https://s2geometry.io/devguide/s2cell_hierarchy).

## <a name="syntax"></a>Синтаксис

`geo_point_to_s2cell(`*Долгота* `, ` *Широта* `, ` *уровень*`)`

## <a name="arguments"></a>Аргументы

* *Долгота*: значение долготы географического расположения. Долгота *x* будет считаться допустимой, если *x* является вещественным числом, а *x* — в диапазоне [-180, + 180]. 
* *Широта*: значение широты географического расположения. Широта y будет считаться действительной, если y — вещественное число и y в диапазоне [-90, + 90]. 
* *Level*: Необязательный `int` параметр, определяющий запрошенный уровень ячейки. Поддерживаемые значения находятся в диапазоне [0, 30]. Если не указано, используется значение по умолчанию `11` .

## <a name="returns"></a>Результаты

Строковое значение токена ячейки S2 заданного географического расположения. Если координаты или уровни недопустимы, запрос приведет к созданию пустого результата.

> [!NOTE]
>
> * Ячейка S2 может быть полезным средством геопространственной кластеризации.
> * Ячейка S2 имеет 31 уровень иерархии с покрытием области от 85011, 012.19 км ² на высшем уровне 0 до 00.44 cm ² на самом низком уровне 30.
> * Ячейка S2 сохраняет открытую центр ячеек во время увеличения уровня от 0 до 30.
> * Ячейка S2 — это ячейка на сферической поверхности, а ее края — жеодесикс.
> * Вызов функции [geo_s2cell_to_central_point ()](geo-s2cell-to-central-point-function.md) в строке токена уровня S2, вычисленной для долготы x и широты y, не обязательно возвращает x и y.
> * Возможно, два географических расположения очень близки друг к другу, но имеют разные маркеры ячеек S2.

**Приблизительная область охвата ячейки S2 на значение уровня**

Для каждого уровня Размер ячейки S2 аналогичен, но не идентичен. Соседние размеры ячеек, как правило, являются более равными.

|Уровень|Минимальная длина границы случайной ячейки (Великобритания)|Максимальная длина границы случайной ячейки (US)|
|--|--|--|
|0|7842 км|7842 км|
|1|3921 км|5004 км|
|2|1825 км|2489 км|
|3|840 км|1310 км|
|4|432 км|636 км|
|5|210 км|315 км|
|6|108 км|156 км|
|7|54 км|78 км|
|8|27 км|39 км|
|9|14 км|20 км|
|10|7 км|10 км|
|11|3 км|5 км|
|12|1699 м|2 км|
|13|850 м|1225 м|
|14|425 м|613 м|
|15|212 м|306 м|
|16|106 м|153 м|
|17|53 м|77 м|
|18|27 м|38 м|
|19|13 м|19 м|
|20|7 м|10 м|
|21|3 м|5 м|
|22|166 cm|2 м|
|23|83 cm|120 cm|
|24|41 cm|60 cm|
|25|21 cm|30 cm|
|26|10 cm|15 cm|
|27|5 cm|7 cm|
|28|2 cm|4 cm|
|29|12 мм|18 мм|
|30|6 мм|9 мм|

Источник таблицы можно найти [в этом статистическом ресурсе этой ячейки S2](https://s2geometry.io/resources/s2cell_statistics).

См. также [geo_point_to_geohash ()](geo-point-to-geohash-function.md).

## <a name="examples"></a>Примеры

События, агрегированные по s2cell.

:::image type="content" source="images/geo-point-to-s2cell-function/s2cell.png" alt-text="S2cell США":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

В следующем примере выполняется поиск групп координат. Каждая пара координат в группе находится в ячейке S2 с максимальной областью 1632,45 км².

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", 10.1234, 53,
  "B", 10.3579, 53,
  "C", 10.6842, 53,
]
| summarize count = count(),                                        // items per group count
            locations = make_list(location_id)                      // items in the group
            by s2cell = geo_point_to_s2cell(longitude, latitude, 8) // s2 cell of the group
```

| s2cell | count | Расположения |
|--------|-------|-----------|
| 47b1d  | 2     | ["A", "B"] |
| 47ae3  | 1     | ["C"]     |

В следующем примере создается пустой результат из-за неправильного ввода координат.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

В следующем примере создается пустой результат из-за недопустимых входных данных уровня.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

В следующем примере создается пустой результат из-за недопустимых входных данных уровня.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |
