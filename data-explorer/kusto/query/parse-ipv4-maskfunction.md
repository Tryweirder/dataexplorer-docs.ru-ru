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
ms.openlocfilehash: fd6bd97befc376d13573a0a85169524cf01b9d2d
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294616"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

Преобразует входную строку IPv4 и сетевой маски в длинное представление чисел (со знаком 64 бит).

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

**Синтаксис**

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

**Аргументы**

* *`Expr`*: Строковое представление адреса IPv4, которое будет преобразовано в значение long. 
* *`PrefixMask`*: Целое число от 0 до 32, представляющее количество наиболее значимых битов, которые учитываются в учетной записи.

**Возвращает**

Если преобразование выполнено успешно, результатом будет целое число.
Если преобразование не выполнено, результатом будет `null` .
