---
title: geo_point_in_circle () — обозреватель данных Azure
description: В этой статье описывается geo_point_in_circle () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 1e94e8eca72e6cb679a84e7b91ea376b9ec4b29c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347822"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

Вычисляет, находятся ли геопространственные координаты внутри окружности на земле.

## <a name="syntax"></a>Синтаксис

`geo_point_in_circle(`*p_longitude* `, ` *p_latitude* `, ` *pc_longitude* `, ` *pc_latitude* `, ` *c_radius*`)`

## <a name="arguments"></a>Аргументы

* *p_longitude*: значение долготы геопространственной координаты в градусах. Допустимые значения — вещественное число и в диапазоне [-180, + 180].
* *p_latitude*: значение широты для геопространственной координаты в градусах. Допустимые значения — вещественное число и в диапазоне [-90, + 90].
* *pc_longitude*: значение долготы геопространственной координаты по кругу в градусах. Допустимые значения — вещественное число и в диапазоне [-180, + 180].
* *pc_latitude*: значение широты геопространственной координаты в градусах. Допустимые значения — вещественное число и в диапазоне [-90, + 90].
* *c_radius*: круг радиуса в метрах. Допустимое значение должно быть положительным.

## <a name="returns"></a>Результаты

Указывает, находятся ли геопространственные координаты внутри окружности. Если координаты или окружность недопустимы, запрос выдаст результат NULL.

> [!NOTE]
>* Геопространственные координаты обрабатываются как представленные системой координат [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
>* [Геодезическийная база](https://en.wikipedia.org/wiki/Geodetic_datum) , используемая для измерения расстояния на земле, — это сфера.
>* Круг — это сферовая заглушка на земле. Радиус крепления определяется вдоль поверхности сферы.

## <a name="examples"></a>Примеры

Следующий запрос находит все места в области, определяемой следующим кругом: радиус 18 км, центр по адресу [-122,317404, 47,609119].

:::image type="content" source="images/geo-point-in-circle-function/circle-seattle.png" alt-text="Расположений вблизи Сиэтл":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(longitude:real, latitude:real, place:string)
[
    real(-122.317404), 47.609119, 'Seattle',                   // In circle 
    real(-123.497688), 47.458098, 'Olympic National Forest',   // In exterior of circle  
    real(-122.201741), 47.677084, 'Kirkland',                  // In circle
    real(-122.443663), 47.247092, 'Tacoma',                    // In exterior of circle
    real(-122.121975), 47.671345, 'Redmond',                   // In circle
]
| where geo_point_in_circle(longitude, latitude, -122.317404, 47.609119, 18000)
| project place
```

|place|
|---|
|Seattle|
|Kirkland|
|Redmond|

События в Орландо. События фильтруются по 100 км в Орландо и объединяются по типу события и хэшу.

:::image type="content" source="images/geo-point-in-circle-function/orlando-storm-events.png" alt-text="События в Орландо":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

В следующем примере показаны посылки для Нью-Йорка в пределах 10 метров определенного местоположения. Соответствующие операции отправки объединяются по хэшу.

:::image type="content" source="images/geo-point-in-circle-function/circle-junction.png" alt-text="Ближайшие такси":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

В следующем примере возвращается значение true.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

В следующем примере возвращается значение false.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

В следующем примере возвращается результат NULL из-за неправильного ввода координат.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

В следующем примере возвращается результат NULL из-за недопустимых входных данных радиуса круга.

```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||
