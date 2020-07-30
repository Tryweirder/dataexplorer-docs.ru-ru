---
title: binary_shift_left () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_shift_left () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 46d18bb5d1f661c5346f5ff825c9597088d3f5cf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349046"
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