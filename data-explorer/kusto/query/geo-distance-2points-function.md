---
title: geo_distance_2points () — обозреватель данных Azure
description: В этой статье описывается geo_distance_2points () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 0e8d57e76b0dfa45003f541b54360cb4ac414646
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347890"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

Вычисляет самое короткое расстояние между двумя геопространственными координатами на земле.

## <a name="syntax"></a>Синтаксис

`geo_distance_2points(`*p1_longitude* `, ` *p1_latitude* `, ` *p2_longitude* `, ` *p2_latitude*`)`

## <a name="arguments"></a>Аргументы

* *p1_longitude*: первая геопространственной координата, значение долготы в градусах. Допустимые значения — вещественное число и в диапазоне [-180, + 180].
* *p1_latitude*: первая геопространственной координата, значение широты в градусах. Допустимые значения — вещественное число и в диапазоне [-90, + 90].
* *p2_longitude*: Вторая Геопространственное координата, значение долготы в градусах. Допустимые значения — вещественное число и в диапазоне [-180, + 180].
* *p2_latitude*: Вторая Геопространственное координата, значение широты в градусах. Допустимые значения — вещественное число и в диапазоне [-90, + 90].

## <a name="returns"></a>Возвращаемое значение

Кратчайшее расстояние (в метрах) между двумя географическими расположениями на земле. Если координаты недопустимы, запрос приведет к результату NULL.

> [!NOTE]
> * Геопространственные координаты обрабатываются как представленные системой координат [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
> * [Геодезическийная база](https://en.wikipedia.org/wiki/Geodetic_datum) , используемая для измерения расстояния на земле, — это сфера.

## <a name="examples"></a>Примеры

В следующем примере выполняется поиск кратчайшего расстояния между Сиэтле и Лос-Анджелес.

:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="Расстояние между Сиэтлом и Лос-Анджелес":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754,35197381   |

Ниже приведено приближение кратчайшего пути из Сиэтле в Лондон. Линия состоит из координат на LineString, а также в пределах 500 метров от него.

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="От Сиэтле до Лондон LineString":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

В следующем примере выполняется поиск всех строк, в которых самое короткое расстояние между двумя координатами находится в диапазоне от 1 до 11 метров.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

В следующем примере возвращается результат NULL из-за неправильного ввода координат.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |
