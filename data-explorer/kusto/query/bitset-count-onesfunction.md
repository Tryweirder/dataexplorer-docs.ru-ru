---
title: bitset_count_ones () — обозреватель данных Azure
description: В этой статье описывается bitset_count_ones () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: f8abb1683a2f15f012e9a9271681688c19901af0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227607"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Возвращает число битов набора в двоичном представлении числа.

```kusto
bitset_count_ones(42)
```

**Синтаксис**

`bitset_count_ones(`*num1*' ') '

**Аргументы**

* *num1*: длинное или целое число.

**Возвращает**

Возвращает число битов набора в двоичном представлении числа.

**Пример**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|шаблоны|
|---|
|3|
