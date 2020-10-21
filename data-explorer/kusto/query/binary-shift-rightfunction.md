---
title: binary_shift_right () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_shift_right () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d33ecb954a7e1e6d0c9c39bdbf057d284affd22b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243520"
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