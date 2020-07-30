---
title: geo_s2cell_to_central_point () — обозреватель данных Azure
description: В этой статье описывается geo_s2cell_to_central_point () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 30075b5a75e273061423a6f1540f44947ef93cec
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347720"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

Вычисляет геопространственные координаты, представляющие центр ячейки S2.

Узнайте больше о [иерархии ячеек S2](https://s2geometry.io/devguide/s2cell_hierarchy).

## <a name="syntax"></a>Синтаксис

`geo_s2cell_to_central_point(`*s2cell*`)`

## <a name="arguments"></a>Аргументы

*s2cell*: строковое значение токена ячейки S2 в том виде, в котором оно было вычислено [geo_point_to_s2cell ()](geo-point-to-s2cell-function.md). Максимальная длина строки токена ячейки S2 составляет 16 символов.

## <a name="returns"></a>Возвращаемое значение

Значения геопространственных координат в [формате геоjson](https://tools.ietf.org/html/rfc7946) и [динамического](./scalar-data-types/dynamic.md) типа данных. Если токен ячейки S2 является недопустимым, запрос приведет к результату NULL.

> [!NOTE]
> В формате геоjson сначала указывается Долгота и Широта секунд.

## <a name="examples"></a>Примеры

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|Координаты|долгота|широта|
|---|---|---|---|
|{<br>  "тип": "точка",<br>  "координаты": [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

В следующем примере возвращается результат NULL из-за недопустимого ввода токена ячейки S2.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||
