---
title: binary_xor () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_xor () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f988fa3d14dab3188bf96825615972995291655
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349012"
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