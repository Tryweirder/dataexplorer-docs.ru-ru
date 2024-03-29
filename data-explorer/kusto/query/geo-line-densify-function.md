---
title: geo_line_densify () — обозреватель данных Azure
description: В этой статье описывается geo_line_densify () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: f86ec0349b4e84215e9b2fdff33b2d705967bcac
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452822"
---
# <a name="geo_line_densify"></a>geo_line_densify()

Преобразует плоские линии или многострочные границы в жеодесикс, добавляя промежуточные точки.

## <a name="syntax"></a>Синтаксис

`geo_line_densify(`*lineString* `, ` *допуск*`)`

## <a name="arguments"></a>Аргументы

* *lineString*: Line или Multiline в [формате геоjson](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных.
* *допуск*: необязательный числ, определяющий максимальное расстояние в метрах между исходной плоской границей и преобразованной пограничной цепочкой геодезические. Поддерживаемые значения находятся в диапазоне [0,1, 10000]. Если не указано, используется значение по умолчанию `10` .

## <a name="returns"></a>Возвращаемое значение

Денсифиед строка в [формате геоjson](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных. Если строка или допуск являются недопустимыми, запрос выдаст результат NULL.

> [!NOTE]
> * Геопространственные координаты обрабатываются как представленные системой координат [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) .

**Определение LineString**

Dynamic ({"Type": "LineString", "координаты": [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

Dynamic ({"Type": "MultiLineString", "координаты": [[line_1, line_2,..., line_N]]})

* Массив координат LineString должен содержать не менее двух записей.
* Координаты [Долгота, Широта] должны быть допустимыми. Долгота должна быть вещественным числом в диапазоне [-180, + 180], а Широта должно быть вещественным числом в диапазоне [-90, + 90].
* Длина границы должна быть меньше 180 градусов. Будет выбрано самое короткое ребро между двумя вершинами.

**Учитывая**

* Максимальное число точек в строке денсифиед ограничено 10485760.
* Хранение строк в [динамическом](./scalar-data-types/dynamic.md) формате имеет ограничения на размер.

**Причины для использования**

* [Формат геоjson](https://tools.ietf.org/html/rfc7946) определяет ребро между двумя точками в виде прямой декартовой линией.
* Решение об использовании геодезические или плоских кромок может зависеть от набора данных и особенно относится к длинным краям.

## <a name="examples"></a>Примеры

В следующем примере денсифиес дорожка в острове манхэттенское. Ребро является коротким, а расстояние между плоским ребром и его аналогом геодезические меньше, чем расстояние, указанное в параметре "Допуск". Таким образом, результат остается неизменным.

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[-73.949247, 40.796860],[-73.973017, 40.764323]]})))
```

|densified_line|
|---|
|{"Type": "LineString", "координаты": [[-73,949247, 40,796860], [-73,973017, 40,764323]]}|

В следующем примере денсифиес граница ~ 130km length.

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[50, 50], [51, 51]]})))
```

|densified_line|
|---|
|{"Type": "LineString", "координаты": [[50, 50], [50.125, 50.125], [50.25, 50.25], [50.375, 50.375], [50.5, 50.5], [50.625, 50.625], [50.75, 50.75], [50.875, 50.875], [51, 51]]}|

В следующем примере возвращается результат NULL из-за неправильного ввода координат.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[300,1],[1,1]]}))
```

|densified_line|
|---|
||

В следующем примере возвращается результат NULL из-за недопустимых входных данных о допуске.

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[1,1],[2,2]]}), 0)
```

|densified_line|
|---|
||
