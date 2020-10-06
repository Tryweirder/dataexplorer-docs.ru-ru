---
title: Case () — Azure обозреватель данных
description: В этой статье описывается пример () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 087ff2fc38f3b72e4abdbb86ce4b7ac98a5569e6
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91765370"
---
# <a name="case"></a>case()

Вычисляет список предикатов и возвращает первое результирующее выражение, предикат которого удовлетворен.

Если ни один из предикатов `true` не возвращает, возвращается результат последнего выражения ( `else` ).
Все нечетные аргументы (число начинается с 1) должны быть выражениями, результатом вычисления которых является  `boolean` значение.
Все даже аргументы ( `then` s) и последний аргумент ( `else` ) должны иметь один и тот же тип.

## <a name="syntax"></a>Синтаксис

`case(`*predicate_1*, *then_1*, *predicate_2*, *then_2*, *predicate_3*, *then_3*, *else*`)`

## <a name="arguments"></a>Аргументы

* *predicate_i*: выражение, результатом вычисления которого является `boolean` значение.
* *then_i*: выражение, которое вычисляется, и его значение возвращается из функции, если *predicate_i* является первым предикатом, результатом которого является `true` .
* *else*: выражение, которое вычисляется, и его значение возвращается из функции, если ни один из *predicate_i* не имеет значение `true` .

## <a name="returns"></a>Возвращаемое значение

Значение первого *then_i* , для которого *predicate_i* вычисляется `true` , или значение *else* , если ни один из предикатов не удовлетворен.

## <a name="example"></a>Например, .

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|11|Большой|
|13|Большой|
|15|Большой|
