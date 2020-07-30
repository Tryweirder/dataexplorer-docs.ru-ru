---
title: make_timespan () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_timespan () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3904f852fdf813d8b2aff264d6b1bc0019335d78
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346955"
---
# <a name="make_timespan"></a>make_timespan()

Создает скалярное значение [TimeSpan](./scalar-data-types/timespan.md) за указанный период времени.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

## <a name="syntax"></a>Синтаксис

`make_timespan(`*час*,*минута*`)`

`make_timespan(`*час*,*минута*,*секунда*`)`

`make_timespan(`*день*,*час*,*минута*,*секунда*`)`

## <a name="arguments"></a>Аргументы

* *день*: день (положительное целое значение)
* *час*: час (целое значение, от 0 до 23)
* *минута*: минута (целочисленное значение от 0 до 59)
* *секунда*: секунда (вещественное значение, от 0 до 59,9999999)

## <a name="returns"></a>Возвращаемое значение

Если создание прошло успешно, результатом будет значение [TimeSpan](./scalar-data-types/timespan.md) , в противном случае результатом будет NULL.
 
## <a name="example"></a>Пример

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|Интервал времени|
|---|
|1.12:30:55.1230000|


