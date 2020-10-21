---
title: binary_or () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_or () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 97144b244fb6fea5ac218f6160d8aa9e95f50aa4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247876"
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