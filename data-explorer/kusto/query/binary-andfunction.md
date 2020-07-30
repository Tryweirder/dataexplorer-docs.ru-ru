---
title: binary_and () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается binary_and () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 611580aebbfd974377f5a22ec904bbbcdbeb6e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349097"
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