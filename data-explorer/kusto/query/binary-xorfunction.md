---
title: binary_xor() - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается binary_xor () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2f487aa44f8885cbb443c31b8bb3a503e1a76fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517533"
---
# <a name="binary_xor"></a>binary_xor()

Возвращает результат битовой `xor` работы двух значений.

```kusto
binary_xor(x,y)
```

**Синтаксис**

`binary_xor(`*num1* `,` *num2*`)`

**Аргументы**

* *num1*, *num2*: длинные числа.

**Возвращает**

Возвращает логическую операцию XOR на пару чисел: num1 и num2.