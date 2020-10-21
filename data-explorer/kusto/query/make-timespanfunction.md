---
title: make_timespan () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается make_timespan () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93b8ed12bcfb83c39964f820e61f928357af62c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253189"
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

## <a name="returns"></a>Результаты

Если создание прошло успешно, результатом будет значение [TimeSpan](./scalar-data-types/timespan.md) , в противном случае результатом будет NULL.
 
## <a name="example"></a>Пример

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|Интервал времени|
|---|
|1.12:30:55.1230000|


