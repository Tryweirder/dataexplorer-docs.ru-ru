---
title: geo_s2cell_to_central_point() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается geo_s2cell_to_central_point () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 624d163b508768d0a5316ee3ec62a12b11942176
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514439"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

Вычисляет геопространственные координаты, представляющие центр ячейки S2.

Для получения дополнительной информации о S2 ячейки, нажмите [здесь](http://s2geometry.io/devguide/s2cell_hierarchy).

**Синтаксис**

`geo_s2cell_to_central_point(`*s2cell*`)`

**Аргументы**

*s2cell*: Значение токенов s2 Cell, как это было рассчитано [geo_point_to_s2cell()](geo-point-to-s2cell-function.md). Максимальная длина токена S2 Cell составляет 16 символов.

**Возвращает**

Значения геопространственных координат в [формате GeoJSON](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных. Если токен ячейки S2 недействителен, запрос даст нулевой результат.

> [!NOTE]
> Формат GeoJSON определяет длину первой и широты второй.

**Примеры**

```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|координаты|долгота|широта|
|---|---|---|---|
|{<br>  "тип": "Точка",<br>  "Координаты":<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

Следующий пример возвращает нулевой результат из-за недействительного ввода маркера ячейки S2.
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||