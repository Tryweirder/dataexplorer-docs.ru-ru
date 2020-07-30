---
title: binary_shift_right () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_shift_right () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 96da8894aa4320a2d423d072acc048994463a7b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349029"
---
# <a name="binary_shift_right"></a>binary_shift_right()

Возвращает бинарную операцию сдвига вправо для пары чисел.

```kusto
binary_shift_right(x,y) 
```

## <a name="syntax"></a>Синтаксис

`binary_shift_right(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Аргументы

* *num1*, *num2*: длинные числа.

## <a name="returns"></a>Результаты

Возвращает бинарную операцию сдвига вправо для пары чисел: num1 >>  (num2% 64).
Если n является отрицательным, возвращается значение NULL.