---
title: binary_or () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_or () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6903ee36e29551e7af6d08e686c1189e0c0125f3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349063"
---
# <a name="binary_or"></a>binary_or()

Возвращает результат побитовой `or` операции двух значений. 

```kusto
binary_or(x,y)
```

## <a name="syntax"></a>Синтаксис

`binary_or(`*num1* `,` *num2*`)`

## <a name="arguments"></a>Аргументы

* *num1*, *num2*: длинные числа.

## <a name="returns"></a>Результаты

Возвращает логическую операцию или для пары чисел: num1 | NUM2.