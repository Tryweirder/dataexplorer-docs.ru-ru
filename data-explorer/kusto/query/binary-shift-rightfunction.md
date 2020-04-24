---
title: binary_shift_right () - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает binary_shift_right () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94c8c695f1c5d16ee0a7e3a92882486b8a8ef5d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517550"
---
# <a name="binary_shift_right"></a>binary_shift_right()

Возвращает двоичную операцию сдвига на пару чисел.

```kusto
binary_shift_right(x,y) 
```

**Синтаксис**

`binary_shift_right(`*num1* `,` *num2*`)`

**Аргументы**

* *num1*, *num2*: длинные числа.

**Возвращает**

Возвращает правую операцию бинарного сдвига на пару чисел: num1 >>  (num2%64).
Если n является отрицательным, возвращается значение NULL.