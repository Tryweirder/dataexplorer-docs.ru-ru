---
title: bitset_count_ones () — обозреватель данных Azure
description: В этой статье описывается bitset_count_ones () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: fb14ffa5807a61ade913631c7d6da6b23edc26d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245337"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Возвращает число битов набора в двоичном представлении числа.

```kusto
bitset_count_ones(42)
```

## <a name="syntax"></a>Синтаксис

`bitset_count_ones(`*num1*' ') '

## <a name="arguments"></a>Аргументы

* *num1*: длинное или целое число.

## <a name="returns"></a>Результаты

Возвращает число битов набора в двоичном представлении числа.

## <a name="example"></a>Пример

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|шаблоны|
|---|
|3|
