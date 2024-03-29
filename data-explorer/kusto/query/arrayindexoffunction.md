---
title: array_index_of () — обозреватель данных Azure
description: В этой статье описывается array_index_of () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: b06ae72c872575f7fc45741c4b023f82440815db
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246876"
---
# <a name="array_index_of"></a>array_index_of()

Выполняет поиск заданного элемента в массиве и возвращает его позицию.

## <a name="syntax"></a>Синтаксис

`array_index_of(`*массив*,*значение*`)`

## <a name="arguments"></a>Аргументы

* *массив*: входной массив для поиска.
* *значение*: искомое значение. Значение должно иметь тип long, Integer, Double, DateTime, TimeSpan, Decimal, String или GUID.

## <a name="returns"></a>Результаты

Отсчитываемая от нуля позиция индекса поиска.
Возвращает-1, если значение не найдено в массиве.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|Результат|
|---|
|3|

## <a name="see-also"></a>См. также раздел

Если требуется только проверить, существует ли значение в массиве, но вы не заинтересованы в его положении, можно использовать [set_has_element ( `arr` , `value` )](sethaselementfunction.md). Эта функция повысит удобочитаемость запроса. Обе функции имеют одинаковую производительность.
