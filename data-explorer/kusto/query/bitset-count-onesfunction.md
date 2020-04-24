---
title: bitset_count_ones () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается bitset_count_ones () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: c23e95ee9f00a0ca173d68d3591ad604b31dfac2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517397"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Возвращает количество наборбит в двоичном представлении числа.

```kusto
bitset_count_ones(42)
```

**Синтаксис**

`bitset_count_ones(`*num1*'')'

**Аргументы**

* *num1*: длинное или целый номер.

**Возвращает**

Возвращает количество наборбит в двоичном представлении числа.

**Пример**

```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|Те|
|---|
|3|