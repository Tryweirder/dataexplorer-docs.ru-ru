---
title: binary_shift_left() - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает binary_shift_left () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15e2bee789e627709ccfedde8eccead7f2578b51
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517567"
---
# <a name="binary_shift_left"></a>binary_shift_left()

Возвращает двоичную смену левой операции на пару чисел.

```kusto
binary_shift_left(x,y)  
```

**Синтаксис**

`binary_shift_left(`*num1* `,` *num2*`)`

**Аргументы**

* *num1*, *num2*: int номера.

**Возвращает**

Возвращает двоичную смену левой операции на пару чисел: num1 <<  (num2%64).
Если n является отрицательным, возвращается значение NULL.