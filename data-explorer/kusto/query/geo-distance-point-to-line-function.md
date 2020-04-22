---
title: geo_distance_point_to_line () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается geo_distance_point_to_line () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 34c6811ed5a51dae5281a4374421df5914c5b263
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663773"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

Вычисляет кратчайшее расстояние между координатами и линией на Земле.

**Синтаксис**

`geo_distance_point_to_line(`*долгота*`, `*широты*`, `*линииString*`)`

**Аргументы**

* *долгота*: Геопространственная координация долготы значение в градусах. Допустимое значение – это реальное число и в диапазоне 180, 180 евро.
* *широта*: Геопространственная координация широты значение в градусах. Допустимое значение – это реальное число и в диапазоне 90, 90 евро.
* *lineString*: Линия в [формате GeoJSON](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных.

**Возвращает**

Самое короткое расстояние, в метрах, между координатами и линией на Земле. Если координаты или строки String недействительны, запрос даст нулевой результат.

> [!NOTE]
> * Геопространственные координаты интерпретируются как представленные системой координат [WGS-84.](https://earth-info.nga.mil/GandG/update/index.php?action=home)
> * [Геодезический дом,](https://en.wikipedia.org/wiki/Geodetic_datum) используемый для измерения расстояния на Земле, является сферой. Края линий являются геодезиками на сфере.

**Определение и ограничения LineString**

динамическая («тип»: «LineString»,«координаты»: «lng_1,lat_1», «lng_2,lat_2» ,..., (lng_N,lat_N)

* Массив координат LineString должен содержать не менее двух записей.
* Координаты «долгота, широта» должны быть действительными там, где долгота – это реальное число в диапазоне »-180, 180», а широта – это реальное число в диапазоне »-90, 90».
* Длина края должна быть менее 180 градусов. Будет выбран кратчайший край между двумя вершинами.

> [!TIP]
> Для повышения производительности используйте буквальные строки.

**Примеры**

Ниже приводится кратчайшее расстояние между аэропортом Северного Лас-Вегаса и близлежащей дорогой.

:::image type="content" source="images/queries/geo/distance_point_to_line.png" alt-text="Расстояние между аэропортом Норт-Лас-Вегас и дорогой":::

```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

Штормовые явления на южном побережье США. События фильтруются на максимальном расстоянии 5 км от определенной береговой линии.

:::image type="content" source="images/queries/geo/us_south_coast_storm_events.png" alt-text="Штормовые явления на южном побережье США":::

```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

NY такси пикапы. Пикапы отфильтровываются на максимальном расстоянии 0,1 м от заданной линии.

:::image type="content" source="images/queries/geo/park_ave_ny_road.png" alt-text="Штормовые явления на южном побережье США":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Следующий пример возвращает нулевой результат из-за недействительного ввода LineString.
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

Следующий пример возвращает нулевой результат из-за недействительных ввода координат.
```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |