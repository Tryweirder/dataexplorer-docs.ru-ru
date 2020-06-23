---
title: bag_merge () — обозреватель данных Azure
description: В этой статье описывается bag_merge () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspod
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 0a23f6ece8be3ba451c1f61a90eb65452b68f9ce
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265069"
---
# <a name="bag_merge"></a>bag_merge()

Объединяет `dynamic` Свойства-контейнеры в `dynamic` контейнер свойств со всеми объединенными свойствами.

**Синтаксис**

`bag_merge(`*bag1* `, ` *bag2* `[` ,` *bag3*, ...])`

**Аргументы**

* *bag1... Багн*: Входное `dynamic` свойство — контейнеры. Функция принимает от 2 до 64 аргументов.

**Возвращает**

Возвращает `dynamic` контейнер свойств. Результаты слияния всех входных объектов контейнера свойств. Если ключ встречается в нескольких входных объектах, будет выбрано произвольное значение (из возможных значений для этого ключа).

**Пример**

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
