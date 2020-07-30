---
title: geo_distance_point_to_line () — обозреватель данных Azure
description: В этой статье описывается geo_distance_point_to_line () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: a7796c14098f773b73bd16735a3d2c9c879c8fd2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347873"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

Вычисляет кратчайшее расстояние между координатами и линией на земле.

## <a name="syntax"></a>Синтаксис

`geo_distance_point_to_line(`*Долгота* `, ` *Широта* `, ` *lineString*`)`

## <a name="arguments"></a>Аргументы

* *Долгота*: значение долготы геопространственной координаты в градусах. Допустимые значения — вещественное число и в диапазоне [-180, + 180].
* *Широта*: Геопространственное значение координаты широты в градусах. Допустимые значения — вещественное число и в диапазоне [-90, + 90].
* *lineString*: строка в [формате геоjson](https://tools.ietf.org/html/rfc7946) и тип [динамических](./scalar-data-types/dynamic.md) данных.

## <a name="returns"></a>Возвращаемое значение

Кратчайшее расстояние в метрах между координатами и линией на земле. Если координата или lineString являются недопустимыми, запрос выдаст результат NULL.

> [!NOTE]
> * Геопространственные координаты обрабатываются как представленные системой координат [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
> * [Геодезическийная база](https://en.wikipedia.org/wiki/Geodetic_datum) , используемая для измерения расстояния на земле, — это сфера. Края линий [жеодесикс](https://en.wikipedia.org/wiki/Geodesic) в сфере.
> * Если границы входных линий являются прямыми декартыми линиями, рассмотрите возможность использования [geo_line_densify ()](geo-line-densify-function.md) для преобразования плоских краев в жеодесикс.

**Определение LineString и ограничения**

Dynamic ({"Type": "LineString", "координаты": [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

* Массив координат LineString должен содержать не менее двух записей.
* Координаты [Долгота, Широта] должны быть допустимыми, где Долгота является вещественным числом в диапазоне [-180, + 180], а Широта — вещественным числом в диапазоне [-90, + 90].
* Длина границы должна быть меньше 180 градусов. Будет выбрано самое короткое ребро между двумя вершинами.

> [!TIP]
> Для повышения производительности используйте литеральные строки.

## <a name="examples"></a>Примеры

В следующем примере выполняется поиск кратчайшего расстояния между руководством по северу Лас-деньги и ближайшим поездкам.

:::image type="content" source="images/geo-distance-point-to-line-function/distance-point-to-line.png" alt-text="Расстояние между аэропортами и поездками Северной Лас":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

События в Южной регион США. События фильтруются по максимальному расстоянию в 5 км от определенной укрепление строки.

:::image type="content" source="images/geo-distance-point-to-line-function/us-south-coast-storm-events.png" alt-text="События в Южной регион США":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Повышена отправке в такси. Раскладки фильтруются по максимальному расстоянию 0,1 м из заданной строки.

:::image type="content" source="images/geo-distance-point-to-line-function/park-ave-ny-road.png" alt-text="События в Южной регион США":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

В следующем примере возвращается результат NULL из-за недопустимых входных данных LineString.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

В следующем примере возвращается результат NULL из-за неправильного ввода координат.

```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |
