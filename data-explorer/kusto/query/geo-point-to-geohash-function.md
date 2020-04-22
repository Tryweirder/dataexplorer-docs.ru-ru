---
title: geo_point_to_geohash () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны geo_point_to_geohash () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: b69d22c56cef4b54a99aa9aa3e9897a2ef177834
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030125"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

Вычисляет значение строки Geohash для географического местоположения.

Для получения дополнительной информации о Geohash, смотрите [здесь](https://en.wikipedia.org/wiki/Geohash).  

**Синтаксис**

`geo_point_to_geohash(`*долготы*`, `*широты*`, `и*точность*`)`

**Аргументы**

* *долгота*: Долгота значение географического положения. Долгота x будет считаться действительной, если x является реальным числом и находится в диапазоне 180, 180 евро. 
* *широта*: Широта значение географического местоположения. Широта у будет считаться действительным, если у является реальным числом, и у находится в диапазоне »-90, 90». 
* *точность*: `int` Необязательный, который определяет запрашиваемую точность. Поддерживаемые значения находятся в диапазоне 1,18. Если не указано, `5` используется значение по умолчанию.

**Возвращает**

Значение строки Geohash данного географического местоположения с запрошенной длиной точности. Если координат или точность недействительны, запрос приведет к пустому результату.

> [!NOTE]
>
> * Geohash может быть полезным инструментом геопространственного кластеризации.
> * Geohash имеет 18 уровней точности с охватом области в диапазоне от 25 миллионов км2 на самом высоком уровне от 1 до 0,6 х 2 на самом низком уровне 18.
> * Общие префиксы геохашев указывают на близость точек друг к другу. Чем длиннее общая приставка, тем ближе два места. Значение точности переводится как длина геохэша.
> * Geohash является прямоугольной области на поверхности самолета.
> * Ссылаясь на [функцию geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md) на строке geohash, которая была рассчитана на долготу x и широты y не обязательно вернется x и y.
> * Из-за определения Geohash, возможно, что два географических местоположения очень близко друг к другу, но имеют различные коды Geohash.

**Geohash прямоугольной области покрытия на точность значения:**

| Точность | Ширина     | Высота:    |
|----------|-----------|-----------|
| 1        | 5000 км   | 5000 км   |
| 2        | 1250 км   | 625 км    |
| 3        | 156.25 км. | 156.25 км. |
| 4        | 39.06 км.  | 19.53 км  |
| 5        | 4.88 км   | 4.88 км   |
| 6        | 1.22 км   | 0.61 км   |
| 7        | 152,59 м  | 152,59 м  |
| 8        | 38,15 м   | 19,07 м   |
| 9        | 4,77 м    | 4,77 м    |
| 10       | 1,19 м    | 0,59 м    |
| 11       | 149,01 мм | 149,01 мм |
| 12       | 37,25 мм  | 18,63 мм  |
| 13       | 4,66 мм   | 4,66 мм   |
| 14       | 1,16 мм   | 0,58 мм   |
| 15       | 145.52 μ  | 145.52 μ  |
| 16       | 36,28 х х х   | 18.19   |
| 17       | 4,55 х х х    | 4,55 х х х    |
| 18       | 1,14 х х    | 0,57    |

Смотрите также [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md).

**Примеры**

Сша штормовые события, агрегированные геохаш.

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="Геохаш США":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| geohash      |
|--------------|
| xn76m27ty9g4 |

```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|geohash|
|---|
|dhwfz15h|

В следующем примере находятся группы координат. Каждая пара координат в группе находятся в прямоугольной области 4,88 км на 4,88 км.
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

| geohash | count | Расположения  |
|---------|-------|------------|
| c23n8   | 2     | "А", "Б" |
| c23n9   | 1     | ("С"      |

Следующий пример производит пустой результат из-за недействительных ввода координат.
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| geohash |
|---------|
|         |

Следующий пример производит пустой результат из-за недействительного ввода точности.
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| geohash |
|---------|
|         |