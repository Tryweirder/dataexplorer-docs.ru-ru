---
title: bag_merge () — обозреватель данных Azure
description: В этой статье описывается bag_merge () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 66a05cdc03b155b8fceace0af8d86807a10d0da4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349369"
---
# <a name="bag_merge"></a>bag_merge()

Объединяет `dynamic` Свойства-контейнеры в `dynamic` контейнер свойств со всеми объединенными свойствами.

## <a name="syntax"></a>Синтаксис

`bag_merge(`*bag1* `, ` *bag2* `[` ,` *bag3*, ...])`

## <a name="arguments"></a>Аргументы

* *bag1... Багн*: Входное `dynamic` свойство — контейнеры. Функция принимает от 2 до 64 аргументов.

## <a name="returns"></a>Результаты

Возвращает `dynamic` контейнер свойств. Результаты слияния всех входных объектов контейнера свойств. Если ключ встречается в нескольких входных объектах, будет выбрано произвольное значение (из возможных значений для этого ключа).

## <a name="example"></a>Пример

Выражение:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result = bag_merge(
   dynamic({'A1':12, 'B1':2, 'C1':3}),
   dynamic({'A2':81, 'B2':82, 'A1':1}))
```

|result|
|---|
|{<br>  "A1": 12,<br>  "B1": 2,<br>  "C1": 3,<br>  "A2": 81,<br>  "B2": 82<br>}|
