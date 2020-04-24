---
title: binary_and () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается binary_and () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c5501218c1ddb69a73f685fda78f3b3482afb5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517686"
---
# <a name="binary_and"></a>binary_and()

Возвращает результат операции bitwise `and` между двумя значениями.

```kusto
binary_and(x,y) 
```

**Синтаксис**

`binary_and(`*num1* `,` *num2*`)`

**Аргументы**

* *num1*, *num2*: длинные числа.

**Возвращает**

Возвращает логическую и операцию на пару чисел: num1 & num2.