---
title: geo_geohash_to_central_point() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны geo_geohash_to_central_point () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a71265b58cffcec2fcf9d6ac8d412c8dd44866f4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514643"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Вычисляет геопространственные координаты, представляющие центр прямоугольной области Geohash.

Для получения дополнительной информации о Geohash, [см.](https://en.wikipedia.org/wiki/Geohash)  

**Синтаксис**

`geo_geohash_to_central_point(`*geohash*`)`

**Аргументы**

*geohash*: Geohash значение строки, как это было рассчитано [geo_point_to_geohash()](geo-point-to-geohash-function.md). Строка геохэша может быть от 1 до 18 символов.

**Возвращает**

Значения геопространственных координат в [формате GeoJSON](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных. Если геохаш недействителен, запрос даст нулевой результат.

> [!NOTE]
> Формат GeoJSON определяет длину первой и широты второй.

**Примеры**

```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|координаты|долгота|широта|
|---|---|---|---|
|{<br>  "тип": "Точка",<br>  "Координаты":<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

Следующий пример возвращает нулевой результат из-за недействительного геохаса ввода.

```kusto
print geohash = geo_geohash_to_central_point("a")
```

|geohash|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>Пример: Создание глубоких ссылок на местоположение для Bing Maps

Вы можете использовать значение Geohash для создания URL-адреса глубокой связи с Картами Bing, указывая на центральную точку Geohash:

```kusto
// Use string concatenation to create Bing Map deep-link URL from a geo-point
let point_to_map_url = (_point:dynamic, _title:string) 
{
    strcat('https://www.bing.com/maps?sp=point.', _point.coordinates[1] ,'_', _point.coordinates[0], '_', url_encode(_title)) 
};
// Convert geohash to center point, and then use 'point_to_map_url' to create Bing Map deep-link
let geohash_to_map_url = (_geohash:string, _title:string)
{
    point_to_map_url(geo_geohash_to_central_point(_geohash), _title)
};
print geohash = 'sv8wzvy7'
| extend url = geohash_to_map_url(geohash, "You are here")
```

|geohash|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|