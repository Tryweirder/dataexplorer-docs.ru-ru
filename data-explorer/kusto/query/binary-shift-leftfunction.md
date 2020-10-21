---
title: binary_shift_left () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_shift_left () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f01f0178fa850009f6298446b02c9d4bd913bf8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252605"
---
# <a name="binary_shift_left"></a>binary_shift_left()

Возвращает бинарную операцию сдвига влево для пары чисел.

```kusto
binary_shift_left(x,y)  
```

## <a name="syntax"></a>Синтаксис

`binary_shift_left(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Аргументы

* *num1*, *num2*: int Numbers.

## <a name="returns"></a>Результаты

Возвращает бинарную операцию сдвига влево для пары чисел: num1 <<  (num2% 64).
Если n является отрицательным, возвращается значение NULL.