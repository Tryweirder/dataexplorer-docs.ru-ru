---
title: Тип данных о временном расписании - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается тип данных о временном промежутке времени в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31a0bfafed817ffaf531cffdcb844da8a357531f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509611"
---
# <a name="the-timespan-data-type"></a>Тип данных по временному поясу

Тип `timespan` `time`данных ( ) представляет интервал времени.

## <a name="timespan-literals"></a>промежуток времени в буквах

`timespan` Буквы типа имеют `timespan(` *значение*`)`синтаксиса, где ряд форматов поддерживается по *стоимости,* как указано в следующей таблице:

|||
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

Специальная `time(null)` форма - [это нулевая величина.](null-values.md)

## <a name="timespan-operators"></a>Операторы временной промежутки

Два значения типа `timespan` могут быть добавлены, вычтены и разделены.
Последняя операция возвращает значение `real` типа, представляющее дробное количество раз, когда одно значение может соответствовать другому.

## <a name="examples"></a>Примеры

Следующий пример вычисляет, сколько секунд в день несколькими способами:

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```