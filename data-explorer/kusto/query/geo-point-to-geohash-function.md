---
title: geo_point_to_geohash () — обозреватель данных Azure
description: В этой статье описывается geo_point_to_geohash () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 7998726637a7d19413954a509dd0ad9b34202f03
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347771"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

Вычисляет строковое значение геохэша для географического расположения.

Подробнее о [геохэше](https://en.wikipedia.org/wiki/Geohash).  

## <a name="syntax"></a>Синтаксис

`geo_point_to_geohash(`*Долгота* `, ` *Широта* `, ` [*точность*]`)`

## <a name="arguments"></a>Аргументы

* *Долгота*: значение долготы географического расположения. Долгота x будет считаться допустимой, если x является вещественным числом и находится в диапазоне [-180, + 180]. 
* *Широта*: значение широты географического расположения. Широта y будет считаться действительной, если y — вещественное число, а y — в диапазоне [-90, + 90]. 
* *точность*. необязательный `int` параметр, определяющий запрашиваемую точность. Поддерживаемые значения находятся в диапазоне [1, 18]. Если не указано, используется значение по умолчанию `5` .

## <a name="returns"></a>Возвращаемое значение

Строковое значение геохэш-значения заданного географического расположения с запрошенной длиной точности. Если координата или точность неправильны, запрос приведет к созданию пустого результата.

> [!NOTE]
>
> * Геохэш может быть полезным средством геопространственной кластеризации.
> * Геохэш имеет 18 уровней точности с покрытием в области от 25 000 000 км² на высшем уровне 1 до 0,6 μ ² на самом низком уровне 18.
> * Распространенные префиксы геохэш-кода обозначают расстояние между точками. Чем длиннее общий префикс, тем ближе два места. Значение точности преобразуется в длину по геохэшу.
> * Геохэш — это прямоугольная область на поверхности плоскости.
> * Вызов функции [geo_geohash_to_central_point ()](geo-geohash-to-central-point-function.md) в строке геохэш-кода, вычисленной по долготе x и широте y, не обязательно возвращает x и y.
> * Из-за геохэш-определения возможно, что два географических расположения очень близки друг к другу, но имеют разные геохэш-коды.

**Покрытие прямоугольной области геохэш-кода на значение точности:**

| Точность | Ширина     | Высота:    |
|----------|-----------|-----------|
| 1        | 5000 км   | 5000 км   |
| 2        | 1250 км   | 625 км    |
| 3        | 156,25 км | 156,25 км |
| 4        | 39,06 км  | 19,53 км  |
| 5        | 4,88 км   | 4,88 км   |
| 6        | 1,22 км   | 0,61 км   |
| 7        | 152,59 м  | 152,59 м  |
| 8        | 38,15 м   | 19,07 м   |
| 9        | 4,77 м    | 4,77 м    |
| 10       | 1,19 м    | 0,59 м    |
| 11       | 149,01 мм | 149,01 мм |
| 12       | 37,25 мм  | 18,63 мм  |
| 13       | 4,66 мм   | 4,66 мм   |
| 14       | 1,16 мм   | 0,58 мм   |
| 15       | 145,52 μ  | 145,52 μ  |
| 16       | 36,28 μ   | 18,19 μ   |
| 17       | 4,55 μ    | 4,55 μ    |
| 18       | 1,14 μ    | 0,57 μ    |

См. также [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md).

## <a name="examples"></a>Примеры

События, агрегированные по геохэшу.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="Геохэш США":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| геохэш      |
|--------------|
| xn76m27ty9g4 |

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|геохэш|
|---|
|dhwfz15h|

В следующем примере выполняется поиск групп координат. Каждая пара координат в группе находится в прямоугольной области 4,88 км на 4,88 км.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", double(-122.303404), 47.570482,
  "B", double(-122.304745), 47.567052,
  "C", double(-122.278156), 47.566936,
]
| summarize count = count(),                                          // items per group count
            locations = make_list(location_id)                        // items in the group
            by geohash = geo_point_to_geohash(longitude, latitude)    // geohash of the group
```

| геохэш | count | Расположения  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

В следующем примере создается пустой результат из-за неправильного ввода координат.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| геохэш |
|---------|
|         |

В следующем примере создается пустой результат из-за неправильного ввода точности.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| геохэш |
|---------|
|         |
