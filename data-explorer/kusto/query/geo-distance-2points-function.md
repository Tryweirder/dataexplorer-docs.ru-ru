---
title: geo_distance_2points () - Исследователь данных Azure (англ.) Документы Майкрософт
description: В этой статье описаны geo_distance_2points () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 30a26bbcc57ecde2ac3beeeb1483b953a0cc5820
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030172"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

Вычисляет кратчайшее расстояние между двумя геопространственных координатами на Земле.

**Синтаксис**

`geo_distance_2points(`*p1_longitude*`, `p1_latitude*p2_longitude*`, `*p2_latitude* *p1_latitude p1_latitude*`, ``)`

**Аргументы**

* *p1_longitude*: Первая геопространственная координата, значение долготы в градусах. Допустимое значение – это реальное число и в диапазоне 180, 180 евро.
* *p1_latitude*: Первая геопространственная координата, значение широты в градусах. Допустимое значение – это реальное число и в диапазоне 90, 90 евро.
* *p2_longitude*: Вторая геопространственная координата, значение долготы в градусах. Допустимое значение – это реальное число и в диапазоне 180, 180 евро.
* *p2_latitude*: Вторая геопространственная координата, широта значение в градусах. Допустимое значение – это реальное число и в диапазоне 90, 90 евро.

**Возвращает**

Самое короткое расстояние, в метрах, между двумя географическими точками на Земле. Если координаты недействительны, запрос даст нулевой результат.

> [!NOTE]
> * Геопространственные координаты интерпретируются как представленные системой координат [WGS-84.](https://earth-info.nga.mil/GandG/update/index.php?action=home)
> * [Геодезический дом,](https://en.wikipedia.org/wiki/Geodetic_datum) используемый для измерения расстояния на Земле, является сферой.

**Примеры**

Ниже приведенприведение наименьшее расстояние между Сиэтлом и Лос-Анджелесом.


:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="Расстояние между Сиэтлом и Лос-Анджелесом":::

```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

Вот приближение кратчайшего пути из Сиэтла в Лондон. Линия состоит из координат вдоль LineString и в пределах 500 метров от нее.

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="Сиэтл - Лондон LineString":::

```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

В следующем примере приведены все ряды, в которых кратчайшее расстояние между двумя координатами составляет от 1 до 11 метров.
```kusto
StormEvents
| extend distance_1_to_11m = geo_distance_2points(BeginLon, BeginLat, EndLon, EndLat)
| where distance_1_to_11m between (1 .. 11)
| project distance_1_to_11m
```

| distance_1_to_11m |
|-------------------|
| 10.5723100154958  |
| 7.92153588248414  |

Следующий пример возвращает нулевой результат из-за недействительных ввода координат.
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |