---
title: geo_polygon_densify () — обозреватель данных Azure
description: В этой статье описывается geo_polygon_densify () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: bdb7bda617085ae1a7b3ead60c46c80c883943f4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347788"
---
# <a name="geo_polygon_densify"></a>geo_polygon_densify()

Преобразует многоугольники или многоугольные плоские границы в жеодесикс, добавляя промежуточные точки.

## <a name="syntax"></a>Синтаксис

`geo_polygon_densify(`*многоугольник* `, ` *допуск*`)`

## <a name="arguments"></a>Аргументы

* *многоугольник*: многоугольник или Многоугольный в [формате геоjson](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных.
* *допуск*: необязательный числ, определяющий максимальное расстояние в метрах между исходной плоской границей и преобразованной пограничной цепочкой геодезические. Поддерживаемые значения находятся в диапазоне [0,1, 10000]. Если не указано, используется значение по умолчанию `10` .

## <a name="returns"></a>Результаты

Денсифиед многоугольник в [формате геоjson](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных. Если многоугольник или допуск недопустимы, запрос приведет к результату NULL.

> [!NOTE]
> * Геопространственные координаты обрабатываются как представленные системой координат [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .
> * Многоугольник должен быть правильно определен, но функция не проверяет допустимость многоугольника.

**Определение многоугольника**

Dynamic ({"тип": "Многоугольник", "координаты": [Линеаррингшелл, LinearRingHole_1,..., LinearRingHole_N]})

Dynamic ({"тип": "Многоугольный", "координаты": [[Линеаррингшелл, LinearRingHole_1,..., LinearRingHole_N],..., [Линеаррингшелл, LinearRingHole_1,..., LinearRingHole_M]]})

* Линеаррингшелл является обязательным и определяется как `counterclockwise` упорядоченный массив координат [[lng_1, lat_1],..., [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]]. Может существовать только одна оболочка.
* Линеаррингхоле является необязательным и определяется как `clockwise` упорядоченный массив координат [[lng_1, lat_1],..., [lng_i, lat_i],..., [lng_j, lat_j],..., [lng_1, lat_1]]. Может существовать любое количество внутренних колец и отверстий.
* Вершины LinearRing должны быть разными по крайней мере с тремя координатами. Первая координата должна быть равна последней. Требуется по крайней мере четыре элемента.
* Координаты [Долгота, Широта] должны быть допустимыми. Долгота должна быть вещественным числом в диапазоне [-180, + 180], а Широта — вещественным числом в диапазоне [-90, + 90].
* Линеаррингшелл охватывает не более половины сферы. LinearRing делит сферу на две области. Будет выбрано меньшее из двух регионов.
* Длина LinearRing границы должна быть меньше 180 градусов. Будет выбрано самое короткое ребро между двумя вершинами.

**Ограничения**

* Максимальное число точек в денсифиед многоугольнике ограничено 10485760.
* Хранение многоугольников в [динамическом](./scalar-data-types/dynamic.md) формате имеет ограничения по размеру.
* Денсифинг допустимый Многоугольник может сделать его недействительным. Алгоритм добавляет точки неоднородным образом и, таким образом, может привести к интертвине границ друг к другу.

**Причины для использования**

* [Формат геоjson](https://tools.ietf.org/html/rfc7946) определяет ребро между двумя точками в виде прямой декартовой линией.
* Решение об использовании геодезические или плоских кромок может зависеть от набора данных и особенно относится к длинным краям.

## <a name="examples"></a>Примеры

В следующем примере денсифиес манхэттенское Central парковки. Края являются короткими, а расстояние между плоскими краями и их аналогами геодезические меньше, чем расстояние, указанное в поле допуск. Таким образом, результат остается неизменным.

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[-73.958244,40.800719],[-73.949146,40.79695],[-73.973093,40.764226],[-73.982062,40.768159],[-73.958244,40.800719]]]})))
```

|densified_polygon|
|---|
|{"Type": "Polygon", "координаты": [[[-73.958244, 40.800719], [-73.949146, 40.79695], [-73.973093, 40.764226], [-73.982062, 40.768159], [-73.958244, 40.800719]]]}|

В следующем примере денсифиес два края многоугольника. Длина денсифиедных кромок составляет ~ 110 км

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]})))
```

|densified_polygon|
|---|
|{"тип": "Polygon", "координаты": [[[10, 10], [10.25, 10], [10,5, 10], [10.75, 10], [11, 10], [11, 11], [10.75, 11], [10.5, 11], [10.25, 11], [10, 11], [10, 10]]]}|

В следующем примере возвращается результат NULL из-за неправильного ввода координат.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,900],[11,10],[11,11],[10,11],[10,10]]]}))
```

|densified_polygon|
|---|
||

В следующем примере возвращается результат NULL из-за недопустимых входных данных о допуске.

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]}), 0)
```

|densified_polygon|
|---|
||
