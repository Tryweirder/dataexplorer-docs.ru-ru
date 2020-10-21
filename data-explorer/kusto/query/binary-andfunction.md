---
title: binary_and () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_and () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93318785cff98c7ca024b638e5e90f58cd9cd9d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249369"
---
# <a name="binary_and"></a>binary_and()

Возвращает результат побитовой `and` операции между двумя значениями.

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>Синтаксис

`binary_and(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Аргументы

* *num1*, *num2*: длинные числа.

## <a name="returns"></a>Результаты

Возвращает логическую операцию и для пары чисел: num1 & num2.