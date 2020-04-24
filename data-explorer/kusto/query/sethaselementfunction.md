---
title: set_has_element () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается set_has_element () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 256e01646c6ecd39a8a589299acd6620008ece28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507775"
---
# <a name="set_has_element"></a>set_has_element()

Определяет, содержит ли указанный набор указанный элемент.

**Синтаксис**

`set_has_element(`*массив,**значение*`)`

**Аргументы**

* *массив*: Массив ввода для поиска.
* *значение*: Значение для поиска. Значение должно быть `long`типа, `double` `integer`, `timespan`, `string` `datetime`, `guid`, `decimal`

**Возвращает**

Истинное или ложное в зависимости от того, существует ли значение в массиве.

**Пример**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|Результат|
|---|
|1|

**См. также:**

Если вас также интересует положение, в котором значение существует в массиве, вы можете использовать [array_index_of (arr, значение)](arrayindexoffunction.md). Обе функции одинаковы, производительность мудрым.