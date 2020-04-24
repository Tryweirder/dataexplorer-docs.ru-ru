---
title: binary_or () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны binary_or () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f6d68137ded3b164ca25d82e0c17b6fef6fc1a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517601"
---
# <a name="binary_or"></a>binary_or()

Возвращает результат битовой `or` работы двух значений. 

```kusto
binary_or(x,y)
```

**Синтаксис**

`binary_or(`*num1* `,` *num2*`)`

**Аргументы**

* *num1*, *num2*: длинные числа.

**Возвращает**

Возвращает логическую или операцию на пару чисел: num1 num2.