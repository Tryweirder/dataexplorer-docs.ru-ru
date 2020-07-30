---
title: parse_ipv4_mask () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается функция parse_ipv4_mask () в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 7399bf83369a1b1e1565dd127712faccf7e60830
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346479"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

Преобразует входную строку IPv4 и сетевой маски в длинное представление чисел (со знаком 64 бит).

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

## <a name="syntax"></a>Синтаксис

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

## <a name="arguments"></a>Аргументы

* *`Expr`*: Строковое представление адреса IPv4, которое будет преобразовано в значение long. 
* *`PrefixMask`*: Целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

## <a name="returns"></a>Результаты

Если преобразование выполнено успешно, результатом будет целое число.
Если преобразование не выполнено, результатом будет `null` .
