---
title: geo_geohash_to_central_point () — обозреватель данных Azure
description: В этой статье описывается geo_geohash_to_central_point () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 40f3daa208c3c7ce18252d8c4f7276346206b250
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347856"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Вычисляет геопространственные координаты, представляющие центр прямоугольной области геохэш-кода.

Дополнительные сведения см [`geohash`](https://en.wikipedia.org/wiki/Geohash) . в статье.  

## <a name="syntax"></a>Синтаксис

`geo_geohash_to_central_point(`*геохэш*`)`

## <a name="arguments"></a>Аргументы

*геохэш*: строковое значение геохэш-значения в том виде, в котором оно было вычислено [geo_point_to_geohash ()](geo-point-to-geohash-function.md). Длина строки геохэш-кода может составлять от 1 до 18 символов.

## <a name="returns"></a>Результаты

Значения геопространственных координат в [формате геоjson](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных. Если геохэш является недопустимым, запрос выдает результат NULL.

> [!NOTE]
> В формате геоjson сначала указывается Долгота и Широта секунд.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|Координаты|долгота|широта|
|---|---|---|---|
|{<br>  "тип": "точка",<br>  "координаты": [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

В следующем примере возвращается результат NULL из-за недопустимых входных данных геоалгоритма.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_geohash_to_central_point("a")
```

|геохэш|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>Пример. Создание глубокой ссылки на расположение для карт Bing

Вы можете использовать значение геохэша для создания URL-адреса глубокой ссылки на карты Bing, указав центральную точку хэша.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|геохэш|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|
