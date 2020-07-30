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
ms.openlocfilehash: b8ebef923d1cc67c118317680e1ec414900a469e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348961"
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
