---
title: parse_ipv4_mask () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны parse_ipv4_mask () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 994c1e551d7c38bb1493579bcf10438acd2923f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511787"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

Преобразует строку ввода IPv4 и netmask в длинное представление чине (подписано 64-битным).

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432

parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31) 
```

**Синтаксис**

`parse_ipv4_mask(`*Expr*`, `*PrefixMask*`)`

**Аргументы**

* *Expr*: Строка представления адреса IPv4, который будет преобразован в длинный. 
* *PrefixMask*: Целый ряд от 0 до 32 представляющих количество наиболее значительных битов, которые принимаются во внимание.

**Возвращает**

Если конверсия будет успешной, результат будет длинным числом.
Если конверсия не будет `null`успешной, результат будет .