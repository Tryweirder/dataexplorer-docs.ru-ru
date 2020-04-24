---
title: случай () - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описан случай () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 479c4a99d410a2df7a608531914d7dccfc555e7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517278"
---
# <a name="case"></a>case()

Оценивает список предикатов и возвращает первое выражение результата, предикат которого удовлетворен.

Если ни одно из предикатов не `true`возвращается, `else`результат последнего выражения (the ) возвращается.
Все нечетные аргументы (счет начинается с 1) `boolean` должны быть выражениями, которые оценивают значение.
Все даже аргументы `then`(s) и последний аргумент `else`() должны быть одного и того же типа.

**Синтаксис**

`case(`*predicate_1* `,` *then_1*, *predicate_2* `,` *then_2,* *predicate_3* `,` *then_3,* *другие*`)`

**Аргументы**

* *predicate_i*: Выражение, которое `boolean` оценивает значение.
* *then_i*: Выражение, которое получает оценку и его значение возвращается из функции, если *predicate_i* является первым предикатом, который оценивается в `true`.
* *еще*: выражение, которое получает оценку `true`и его значение возвращается из функции, если ни один из *predicate_i* оценить.

**Возвращает**

Значение первого *then_i,* чья *predicate_i* оценивается `true`в , или стоимость *еще,* если ни один из предикатов удовлетворены.

**Пример**

```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|Размер|bucket|
|---|---|
|1|Малый|
|3|Малый|
|5|Средний|
|7|Средний|
|9|Средний|
|11|большой|
|13|большой|
|15|большой|
