---
title: binary_xor () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_xor () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aecf005556a9997e63ee3547f4e0b23da236cf5d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243465"
---
# <a name="binary_xor"></a>binary_xor()

Возвращает результат побитовой `xor` операции двух значений.

```kusto
binary_xor(x,y)
```

## <a name="syntax"></a>Синтаксис

`binary_xor(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Аргументы

* *num1*, *num2*: длинные числа.

## <a name="returns"></a>Результаты

Возвращает логическую операцию XOR для пары чисел: num1 ^ num2.