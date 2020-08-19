---
title: Тип данных TimeSpan — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается тип данных TimeSpan в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 204076e8ed079dec69cae7080e7d2c50df52a9a6
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610327"
---
# <a name="the-timespan-data-type"></a>Тип данных TimeSpan

`timespan` `time` Тип данных () представляет интервал времени.

## <a name="timespan-literals"></a>литералы TimeSpan

Литералы типа `timespan` имеют значение синтаксиса `timespan(` *value* `)` , где количество форматов поддерживается для *значения*, как показано в следующей таблице:

|Значение|Продолжительность времени|
---|---
`2d`|2 дня
`1.5h`|1,5 часа
`30m`|30 минут
`10s`|10 с
`0.1s`|0,1 с
`100ms`| 100 мс
`10microsecond`|10 микросекунд
`1tick`|100 нс
`time(15 seconds)`|15 секунд
`time(2)`| 2 дня
`time(0.12:34:56.7)`|`0d+12h+34m+56.7s`

Специальная форма `time(null)` — это [значение NULL](null-values.md).

## <a name="timespan-operators"></a>операторы TimeSpan

`timespan`Можно добавлять, вычитать и делить два значения типа.
Последняя операция возвращает значение типа, `real` представляющее дробное число раз, которое одно значение может соответствовать другому.

## <a name="examples"></a>Примеры

В следующем примере вычисляется количество секунд в день несколькими способами.

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```