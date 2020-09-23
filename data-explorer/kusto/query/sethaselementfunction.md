---
title: set_has_element () — обозреватель данных Azure
description: В этой статье описывается set_has_element () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: b62e5032d6f2ccedc2883b6cbccaf7be69e1cebf
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103506"
---
# <a name="set_has_element"></a>set_has_element()

Определяет, содержит ли указанный набор указанный элемент.

## <a name="syntax"></a>Синтаксис

`set_has_element(`*массив*,*значение*`)`

## <a name="arguments"></a>Аргументы

* *массив*: входной массив для поиска.
* *значение*: искомое значение. Значение должно иметь тип `long` , `integer` ,,, `double` `datetime` `timespan` , `decimal` , `string` или `guid` .

## <a name="returns"></a>Результаты

Значение true или false в зависимости от того, существует ли значение в массиве.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Результат|
|---|
|1|

## <a name="see-also"></a>См. также раздел

Используйте [`array_index_of(arr, value)`](arrayindexoffunction.md) для поиска позиции, в которой значение существует в массиве. Обе функции имеют одинаковую производительность.
