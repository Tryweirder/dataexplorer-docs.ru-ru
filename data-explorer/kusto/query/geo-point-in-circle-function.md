---
title: geo_point_in_circle() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается geo_point_in_circle () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: ca3ca8a1ac2299c43888ac827d46c25d02e4999d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663797"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

Вычисляет, находятся ли геопространственные координаты внутри круга на Земле.

**Синтаксис**

`geo_point_in_circle(`*c_radius p_latitude p_longitude*`, `*p_latitude*`, `*pc_longitude*`, `*pc_latitude*`, `*c_radius*`)`

**Аргументы**

* *p_longitude*: Значение долготы геопространственной координаты в градусах. Допустимое значение – это реальное число и в диапазоне 180, 180 евро.
* *p_latitude*: Геопространственная координация широты значение в градусах. Допустимое значение – это реальное число и в диапазоне 90, 90 евро.
* *pc_longitude*: Круг центр геопространственной координации значение долготы в градусах. Допустимое значение – это реальное число и в диапазоне 180, 180 евро.
* *pc_latitude*: центр круга геопространственной координации широты значение в градусах. Допустимое значение – это реальное число и в диапазоне 90, 90 евро.
* *c_radius*: Радиус круга в метрах. Допустимое значение должно быть положительным.

**Возвращает**

Указывает, находятся ли геопространственные координаты внутри круга. Если координаты или круг недействительны, запрос даст нулевой результат.

> [!NOTE]
>* Геопространственные координаты интерпретируются как представленные системой координат [WGS-84.](https://earth-info.nga.mil/GandG/update/index.php?action=home)
>* [Геодезический дом,](https://en.wikipedia.org/wiki/Geodetic_datum) используемый для измерения расстояния на Земле, является сферой.
>* Круг - это сферическая шапка на Земле. Радиус крышки измеряется вдоль поверхности сферы.

**Примеры**

В следующем запросе находятся все места в области, определяемые кругом с радиусом 18 км, центр которого находится на уровне 122.317404, 47.609119 координаты.

:::image type="content" source="images/queries/geo/circle_seattle.png" alt-text="Места возле Сиэтл":::

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

Штормовые события в Орландо. События фильтруются Орландо координаты, в пределах 100 км и агрегируется по типу события и хэш.

:::image type="content" source="images/queries/geo/orlando_storm_events.png" alt-text="Штормовые события в Орландо":::

```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

Следующий пример показывает NY Такси пикапы поблизости некоторое место и в пределах 10 метров. Соответствующие пикапы агрегируются по хэшу.

:::image type="content" source="images/queries/geo/circle_junction.png" alt-text="NY Такси поблизости Пикапы":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

Следующий пример вернется верно.
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

Следующий пример будет возвращаться ложным.
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

Следующий пример возвращает нулевой результат из-за недействительных ввода координат.
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

Следующий пример возвращает нулевой результат из-за недействительного радиуса круга.
```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||